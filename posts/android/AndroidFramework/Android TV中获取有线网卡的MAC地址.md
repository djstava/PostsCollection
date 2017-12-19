### 软硬件环境
* Macbook Pro MGX 72
* Android studio 2.1.2
* Android 5.1.1 

### 前言
在带有Ethernet的Android设备中,在系统信息页面上都会去显示其对应的MAC地址,本文就去解决这个问题。

### 部分代码

    //通过解析这个文件来获取MAC,不同厂家的芯片有可能不同
    private static final String ETH0_MAC_ADDR = "/sys/class/net/eth0/address" ;
    
    /*
    * 获取mac号
    * */
    private String getWireMacAddr() {
        try {
            return readLine(ETH0_MAC_ADDR);
        } catch (IOException e) {
            Log.e(TAG,
                    "IO Exception when getting eth0 mac address",
                    e);
            e.printStackTrace();
            return "unavailable";
        }
    }
    
    private static String readLine(String filename) throws IOException {
        BufferedReader reader = new BufferedReader(new FileReader(filename), 256);
        try {
            return reader.readLine();
        } finally {
            reader.close();
        }
    }