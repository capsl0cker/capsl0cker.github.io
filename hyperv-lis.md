---
layout: default
---

**Currently, this is a DRAFT.**

## 1. Structures

_struct hv_device_ 

在Hyper-V里，这是最基本的设备对象，其他的每个设备都有一个该结构。相当于基类。

_struct net_device_

这个是Linux 内核中对网络设备的描述。

_struct netvsc_device_

在Hyper-V中，该结构表示一个网络设备.

_struct rndis_device_

在HyperV中，网络相关的设备都是用RNDIS(https://en.wikipedia.org/wiki/RNDIS[](Remote Network Driver Interface Specification)) 与Host交互，该结构既是与之相关的设置。

## 2. Relationship

struct netvsc_device {
	...
	struct netvsc_channel chann_table[] {
		...
		struct vmbus_channel *channel;
		struct netvsc_device *net_device;
		struct napi_struct napi;
		...
	}

	...

	struct rndis_device *extension; {
		struct net_device *ndev;
		...
	}

}


hv_device -> device -> driver_data == net_device

struct net_device_context -- private data for netvsc device, at the bottom of net_device.

net_device
net_device_context


## 3. Hyper-V Initialization

start_kernel()
	setup_arch()
		__init init_hypervisor_platorm()	(arch/x86/kernel/cpu/hypervisor.c)
			detect_hypervisor_vendor()
				foreach vendor in array **hypervisors[]**, run its **detect** function
			init x86_init.hyper, x86_platform.hyper, x86_hyper_type
			run XXX.init_platorm() (ms_hyperv_init_platform())
				init **ms_hyperv**, include features, misc_features, hints.
				extract host build information
				register_nmi_handler()
				Setup the hook to get control post apic initialization(hyperv_init())
				hyperv_setup_mmu_ops()
				Setup the IDT for hypervisor callback(alloc_intr_gate(HYPERVISOR_CALLBACK_VECTOR, hyperv_callback_vector))

	late_time_init()(actually, x86_late_time_init())
		x86_init.irqs.intr_mode_init()(apic_intr_mode_init())
			default_setup_apic_routing()
				x86_platform.apic_post_init() (For Hyper-V, it is hyperv_init())
					Setup the hypercall page and enable hypercalls
					hyper_alloc_mmu()
					Register Hyperv-V specific clocksource.


x86_hyper_ms_hyperv = {
	.name = "Micorsoft Hyper-V"
	.detect = ms_hyperv_platform,
	.type = X86_HYPER_MS_HYPERV,
	.init.init_platform = ms_hyperv_init_platrom
};


So when there is a interrupt, hyperv_callback_vector() will be called.  HYPERVSIOR_CALLBACK_VECTOR is 0xf3, the IRQ vector.
If Hyper-V is enabled, it will be defined in arch/x86/entry/entry_64.S by **apicinterrupt3**(macro).
Its actual handler is hyperv_vector_handler()(arch/x86/kernel/cpu/mshyperv.c).

hyperv_vector_handler()
	vmbus_handler()(will be set to 'vmbus_isr' later in register vmbus)


## 4. How Hyper-V driver load

VMBUS is registered as a acpi bus:

```C
static const struct acpi_device_id vmbus_acpi_device_ids[] = {
	{"VMBUS", 0},
	{"VMBus", 0},
	{"", 0},
}
MODULE_DEVICE_TABLE(acpi, vmbus_acpi_driver_ids);

static struct acpi_driver vmbus_acpi_driver = {
         .name = "vmbus",
         .ids = vmbus_acpi_device_ids,
         .ops = {
                 .add = vmbus_acpi_add,
                 .remove = vmbus_acpi_remove,
	},
};
```
So when kernel boots, acpi will enumerate devices/buses. Then hv_acpi_init() will run.

hv_acpi_init()
	acpi_bus_register_driver(&vmbus_acpi_driver);
	vmbus_bus_init()
		hv_init()(allocate hv_context.cpu_context)
		bus_register(&hv_bus)
		hv_setup_vmbus_irq(vmbus_isr)(setup 'vmbus_handler' to 'vmbus_isr')
		hv_synic_alloc()
			allocate 'hv_context.hv_numa_map'
			foreach present cpu
				init msg dispacth tasklet (handler is **vmbus_on_msg_dpc**)
				init clockent device
				allocate synic message page
				allocate synic event page
				allocate post msg page
				init channel list
		vmbus_connect()
		vmbus_request_offers()
		hv_setup_kexec_handler()
		hv_setup_crash_handler()

vmbus_request_offers() will send a  request to get all our pending offers. This usaully happends when bootup,
after Hyper-V setup running environment. so we send a request to get all pending offers.


	

## 5. How interrupt is delivered and processed

INT---> IDT table, find handler entry  --> pre proess --> hyperv_vector_handler()

hyperv_vector_handler()
	vmbus_handler()
		vmbus_isr()

vmbus_isr()
	get synic event flags -> hv_cpu->synic_event_page + VMBUS_MESSAGE_SINT
	if already handled -> vmbus_chan_sched(hv_cpu)
	get synic message page -> hv_cpu->synic_message_page + VMBUS_MESSAGE_SINT
	tasklet_schedule(&hv_cpu->msg_dpc);
	add_interrupt_randomness()
