+++
title = "Beginner guide to eBPF"
description = "Hugo, the world's fastest framework for building websites"
date = "2019-02-28"
aliases = ["about-us", "about-hugo", "contact"]
author = "Hugo Authors"
+++

If you are really curious about knowing different technologies. You will definetily love this technology too.

## What is eBPF??

eBPF is a mechanism to attach your own program or any kind of custom program to a particular events(here events refers to system calls). Each time the event gets triggered by some process, your custom program will run. 

I know learning any thing with examples or practicals make your concept crystal clear. So, what you are waiting for. Let's start with one example or practical.  

## Practical

- **Step:1**

 Make a simple hello world program in c language.
 _Note_: Program should be written in c language.
 
```
int hello_world(void *ctx) {
    bpf_trace_printk("Hello World!! \\n");
    return 0;
}
```

- **Step:2**

Now, the second step is to load your program with the help of `BPF`. Basically, `BPF` will compile your c language program into `bytecode` . For now, please ignore `BPF`.

```# Loading program in BPF
b = BPF(text=program)
```

- **Step:3**
And the third step is to attach your program with any event. So, here we will attach it with clone() or fork() event. Whenever any process is forked or cloned your program will get executed. 

```# Getting system call name for fork or clone
clone = b.get_syscall_fnname("clone")
```

```# Attaching hello world program to an event i.e. clone or fork event.
b.attach_kprobe(event=clone, fn_name="hello_world")
```

Above **attch_kprobe** function is used. Basically `kprobe` in simple terms means all kernel function. `syscall` is known as function or probe in Kernel world. Basically, `kprobe` has  knowledge of all `syscalls` inside the kernel. That's why we called **attach_kprobe** function to bind our `hellow world`(or any custom program) program with `clone` syscall event.

Note: Each and every system calls or syscalls refers as events. In general terminology event is just an action.  

These are main 3 steps. So, finally our program would look like,

`hello_world.py`

```from bcc import BPF
from time import sleep

# Programs print hello world !!
program = """
int hello_world(void *ctx) {
    bpf_trace_printk("Hello World!! \\n");
    return 0;
}
"""
# Loading program in BPF
b = BPF(text=program)

# Getting system call name for fork or clone
clone = b.get_syscall_fnname("clone")

# Attaching function i.e. program to an event i.e. clone event.
b.attach_kprobe(event=clone, fn_name="hello_world")

# printing
b.trace_print()
```**_Note_**: You must need to install [bcc](https://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html) tools. 

Run the above program in separate terminal
```sudo python3 hello_world.py
```

And on the other terminal run any commands like date, ls pwd, cmd, etc and see hello world is being printed on the terminal in which `hello_world.py` program is running.

That's all for this practical. Hope you got enjoyed the practical and got the basic idea.

Let's go back in history to understand from where the concept of `eBPF` arises?

  Basically, `eBPF` is built on the top of classic BPF(cBPF). 
  In the initial days, **Berkeley Packet Filter***(BPF) was 
  used to filter the packets from user space.
  
### But how ??

   By attaching pre-defined `bytecode` program with chains of
   syscalls. In the above example we attached one program to
   one system calls. But in `BPF`, many syscalls were
  attached to different programs.

**_Note_**: In those time `BPF` didn't had mechanism to attach any custom program with `syscalls`. Only only `pre-defined` or `in-built` programs were attched to events. It was totally static in nature. Which means that user can't replace those built-in program with your custom program unlike `eBPF` provides. So, basically `eBPF` is extended version of `BPF` which allows to run custom program in the Kernel.

Now, let's go in more deep and try to understand **the working of eBPF**.
`eBPF` uses bpf() sys calls to take code(i.e. Users customize programs written in c language) from user space and just after program being loaded by `BPF`, it is compiled into `bytecode`. After that it injects the `BPF-bytecode` program into Kernel and bind to the specified event(in above example to `clone` or `fork` system call) with the help of `kprobe`(having knowledge of all system calls inside Kernel). Parallelly , at the same time of injecting program in the Kernel, `BPF-bytecode` programs are checked and verified to make sure it's safe from the perspective of security side.  While execution these `BPF-bytecode` programs are first compiled by `JIT` compiler to native languages or instructions according to the architecture of the computer. And that's how any sandboxed or custom program runs each time when events triggers. And just after execution program is removed from the Kernel space from security point of view. That’s how `eBPF` helps in running user space programs into the Kernel space. This is how `eBPF` helps to add more programmability into Kernel space.

So, whenever any events or system calls is triggered by any process running on your system will result into execution of your custom program(hello world in above example).

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4pqv2xioidlhwe2nn6d9.png)  

## Relating `eBPF` with `Javascript`

`eBPF` does to Linux what `JavaScript` does to HTML. (Sort of.)

### But how??

In initial days of a static HTML website, JavaScript lets you define mini programs that run on events such as clicking of mouse, hover of mouse, scrolling of pages, etc. On any event the corresponding JavaScript program runs in a safe virtual machine in the browser. Similarly with `eBPF`, instead of a fixed kernel, you can now write mini programs that run on events like any syscalls such as forking, executing, reading, writting, etc, which are run in a safe virtual machine in the kernel. In reality, `eBPF` is more like the v8 virtual machine that runs JavaScript, rather than JavaScript itself. eBPF is part of the Linux kernel.

The more beautiful program you add in JavaScript the more attractive and beutiful your website looks. Similarly, it's depends on user which type of programs is added. It depend from use case to use case.

## UseCase of eBPF

- **Networking**

1. Hooks: Traffic Control, sockets, XDP
2. Anti-DDoS
3. Load balancing
4. Routing, overlay, NAT
5. TCP control  

- **Tracing and Monitoring**

1. Hooks: kProbes, uProbes, tracepoints and perf events
2. Inspect, trace, profile kernel, or user space function.
3. Aggregate and correlate metrics in the Kernel , return meaningful date  

- **Others**

1. Security(LSM such as AppArmor)
2. Infrared protocols
3. File System, Storage

Reference:
[Beginner guide to eBPF by LizRice](https://www.youtube.com/watch?v=lrSExTfS-iQ)
[Learn eBPF](https://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)
[eBPF](https://ebpf.io/what-is-ebpf/#what-is-ebpf)
[Keynotes](https://ebpf.io/summit-2020/)
