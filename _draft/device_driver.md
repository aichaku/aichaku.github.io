# Kernel Module init function

## built-in module

typedef int (*initcall_t)(void);

#define __define_initcall(level, fn, id) \
    static initcall_t __initcall_##fn##id __used \
    __attribute__((__section__(".initcall" level ".init"))) = fn

#define device_initcall(fn)     __define_initcall("6", fn, 6)

#define __initcall(fn) device_initcall(fn)

#define module_init(x)  __initcall(x);


module_init( sample_init );
__initcall( sample_init );
device_initcall( sample_init );
__define_initcall("6",sample_init,6)

static initcall_t __initcall_sample_init6 __used
    __attribute__((__section__(".initcall6.init"))) = sample_init

static initcall_t __initcall_sample_init6  = sample_init;
static int (* __initcall_sample_init6 )(void) = sample_init;


start_kernel();
rest_init();
kernel_init();
do_basic_setup();
do_initcalls()

for (fn = __early_initcall_end; fn < __initcall_end; fn++)
        do_one_initcall(*fn);


## Loadable module

$ insmod module2.ko


#define module_init(initfn)
       int init_module(void) __attribute__((alias(#initfn)));

module_init( sample_init );

# License
#define __MODULE_INFO(tag, name, info)                    \
static const char __module_cat(name,__LINE__)[]               \
  __used __attribute__((section(".modinfo"), unused, aligned(1)))     \
  = __stringify(tag) "=" info


#define MODULE_INFO(tag, info) __MODULE_INFO(tag, tag, info)
#define MODULE_LICENSE(_license) MODULE_INFO(license, _license)


MODULE_LICENSE("GPL");
MODULE_INFO(license, "GPL")
__MODULE_INFO(license, license, "GPL")
static const char __mod_license6[] = "license=GPL"
