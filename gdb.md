# GDB依附
一般会在main.m文件里处理:

```

#import <UIKit/UIKit.h>

#import "LZDelegate.h"

#include <unistd.h>

#include <sys/types.h>

#include <sys/sysctl.h>

#include <string.h>

#import <dlfcn.h>

#import <sys/types.h>



static int check_debugger( ) __attribute__((always_inline));

int check_debugger( )

{

    size_t size = sizeof(struct kinfo_proc);

    struct kinfo_proc info;

    int ret, name[4];

    memset(&info, 0, sizeof(struct kinfo_proc));

    name[0] = CTL_KERN;

    name[1] = KERN_PROC;

    name[2] = KERN_PROC_PID;

    name[3] = getpid();

    if ((ret = (sysctl(name, 4, &info, &size, NULL, 0)))) {

        return ret; /* sysctl() failed for some reason */

    }
    return (info.kp_proc.p_flag & P_TRACED) ? 1 : 0;

}

typedef int (*ptrace_ptr_t)(int _request, pid_t _pid, caddr_t _addr, int _data);
#if !defined(PT_DENY_ATTACH)
#define PT_DENY_ATTACH 31
#endif  // !defined(PT_DENY_ATTACH)

void disable_gdb() {
    void* handle = dlopen(0, RTLD_GLOBAL | RTLD_NOW);
    ptrace_ptr_t ptrace_ptr = dlsym(handle, "ptrace");
    ptrace_ptr(PT_DENY_ATTACH, 0, 0, 0);
    dlclose(handle);
}

int main(int argc, char *argv[])
{
#ifndef DEBUG
    disable_gdb();
#endif
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([LZDelegate class]));
    }
}
```

> dlopen： 当path 参数为0是,他会自动查找 $LD_LIBRARY_PATH,$DYLD_LIBRARY_PATH, $DYLD_FALLBACK_LIBRARY_PATH 和 当前工作目录中的动态链接库.



1. ptrace方法可以通过打断点的方法绕过，如下所示：

    ```
    # gdb ?1 ./main

    Reading symbols for shared libraries . done

    (gdb) b ptrace

    Function "ptrace" not defined.

    Make breakpoint pending on future shared library load? (y or [n]) y

    Breakpoint 1 (ptrace) pending.

    (gdb) commands

    Type commands for when breakpoint 1 is hit, one per line.

    End with a line saying just "end".

    >return

    >continue

    >end

    (gdb) run

    Starting program: /private/var/root/main

    Reading symbols for shared libraries .............................. done

    Breakpoint 1 at 0x342afa98

    Pending breakpoint 1 - "ptrace" resolved

    Breakpoint 1, 0x342afa98 in ptrace ()

    I'm doing something really secure here!!
    ```
2. 由于可能被攻击者绕过该方法的调用，在应用的多处增加ptrace函数会提高应用的安全性
