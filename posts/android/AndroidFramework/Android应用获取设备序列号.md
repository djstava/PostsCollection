### 软硬件环境
* Macbook Pro MGX 72
* Android studio 2.1.2
* Android 5.1.1

### 前言
上一篇介绍了如何获取ethernet的MAC地址,对于厂商来讲,除了MAC号,还有一项数据也很重要,那就是机器序列号,它是机器出厂时确定的,也是主要标识,每台机器都不一样。
要想获取到序列号,必须要先知道序列号从哪里来,写到了哪里。一般情况下,序列号都是厂商自己定义的一串字串,字串中的某几位会有不同含义,比如厂商的ID、出厂时间、机器类型等,另外,运营商定义的序列号几乎也是遵循这样的规律。

### 出厂时序列号的烧录
这个是烧录程序做的事情。首先,需要设计好flashmap,一般来讲,会拿出一块独立的分区用来存储这些固化信息,这些信息一经烧录,就不会去改变。android镜像中的boot.img、system.img、userdata.img等等也是存放在这样的分区中。Android系统的烧录,一般会采用fastboot去做,简单、方便、快捷。

### uboot获取序列号
序列号写入到了flash后,uboot就有办法获取到,然后uboot就可以将其以(key,value)的形式传递给kernel,当然传给kernel的参数并非只有序列号,所以如果你需要传一些自定义的数据,同样也可以通过这种方法去实现。在Android中,传递的是androidboot.ro.serialno。

### android app中获取序列号
综上所述,在App中去获取序列号,实际上就是去获取android ro属性中的某个值,明白了这点,事情就好办了。看下面的代码实现

    private String getSerialNumber(){
    
        String serial = null;
    
        try {
    
            Class<?> c =Class.forName("android.os.SystemProperties");
    
            Method get =c.getMethod("get", String.class);
    
            serial = (String)get.invoke(c, "ro.serialno");
    
        } catch (Exception e) {
    
            e.printStackTrace();
    
        }
    
        return serial;
    
    }

### 一点扩展
android中的init.${hardware}.rc,其中$(hardware)是各芯片厂商自己的标识。在这个文件中,可以通过setprop命令设置一些系统属性信息，比如mac、厂商id等，然后在android应用中同样可以通过上述方法去获取相应的值。