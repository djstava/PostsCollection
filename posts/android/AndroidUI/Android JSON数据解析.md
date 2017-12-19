### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.2
* Genymotion模拟器

### 前言
如今的APP很少有不跟服务器打交道的，其中在轻量级的通信交互中，JSON(JavaScript Object Notation)和XML，并称为客户端和服务端交互解决方案的倚天剑和屠龙刀，本文主要介绍JSON相关的知识。

#### JSON对象
JSON中对象包括在一对大括号里面，对象中的每一个item都是一个"key:value"数据对，数据对之间以逗号来分隔。JSON对象的key只能是String类型，value可能是其它类型如number，boolean，null。如下所示

	{
  		"Name" : "Alex",
  		"Sex"  : "Male",
  		"Age"  : "20",
	}

#### JSON数组
JSON数组包括在一对中括号里面，每一个元素都是一个JSON对象，也是以逗号来分隔，示例如下

	[
		{
  		"Name" : "Alex",
  		"Sex"  : "Male",
  		"Age"  : "20",
		},

		{
  		"Name" : "May",
  		"Sex"  : "Female",
  		"Age"  : "19",
		},

		{
  		"Name" : "Mike",
  		"Sex"  : "Male",
  		"Age"  : "21",
		}
	]

### FastJSON的基本用法
FastJSON是alibaba公司开发出来的专门解析JSON的一个框架，根据他们自己的测试，速度非常快，效率非常高，而且没有之一。我们以从聚合数据网站上查询苏州城市天气预报的一个JSON数据为例来说明，获取的URL地址是<http://v.juhe.cn/weather/index?format=2&cityname=%E8%8B%8F%E5%B7%9E&key=6f9276e4d79c5d102bbfb28717beba93>。为确保数据源没有问题，可以在浏览器中打开URL，看看是否有数据。这里再介绍一款Mac下的工具JSON Accelerator，不但可以从网络上抓取,还可以添加http请求头信息，使用起来非常方便。

![fastjson_01](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_01.png)

#### 创建工程
新建工程取名为FastJSONDemo，由于要访问网络，所以需要在AndroidManifest.xml中添加相应权限

	<uses-permission android:name="android.permission.INTERNET" />

#### 代码部分
网络请求部分，这里我们使用volley这个库，Google出品，使用简单。源码下载地址<https://android.googlesource.com/platform/frameworks/volley/>，jar包的下载地址<http://download.csdn.net/detail/djstava/8793421>。下载后把volley的库导入到工程中，UI的话，用了一个TextView来显示接收到的json数据

为了建立一个全局的请求队列，需要在实现一个继承自Application的子类，叫VolleyApplication

	package com.djstava.fastjsondemo;

	import android.app.Application;

	import com.android.volley.RequestQueue;
	import com.android.volley.toolbox.Volley;

	/**
 	* 包名称: com.djstava.fastjsondemo
 	* 创建人: djstava
 	* 创建时间: 15/9/23 下午3:44
 	*/
	public class VolleyApplication extends Application{
    	public static RequestQueue requestQueue;

    	@Override
    	public void onCreate() {
        super.onCreate();

        requestQueue = 	Volley.newRequestQueue(getApplicationContext());
    }

    public static RequestQueue getVolleyQueue() {
        return requestQueue;
    	}
	}

然后在MainActivity中的onStart方法编写请求部分，然后在onStop方法中取消请求

	private static final String JSON_URL = "http://v.juhe.cn/weather/index?format=2&cityname=%E8%8B%8F%E5%B7%9E&key=6f9276e4d79c5d102bbfb28717beba93";
	//字段key是从聚合数据网站上申请过来的，请自行替换

	@Override
    protected void onStart() {
        super.onStart();

        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, JSON_URL, null,
                new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject jsonObject) {
                        try {
                            textView.setText(jsonObject.toString());
                            Log.e(TAG,"result:" + jsonObject.getString("resultcode"));
                        } catch (JSONException e) {
                            e.printStackTrace();
                        }
                    }
                },
                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError volleyError) {
                        Log.e(TAG, volleyError.getMessage());
                    }
                });

        jsonObjectRequest.setTag("fastjson");
        VolleyApplication.requestQueue.add(jsonObjectRequest);
    }

    @Override
    protected void onStop() {
        super.onStop();

        VolleyApplication.requestQueue.cancelAll("fastjson");
    }

别忘了在AndroiManifest.xml文件Application标签中加入

	android:name=".VolleyApplication"

使用Genymotion模拟器运行程序，可以看到获取的数据

![fastjson_02](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_02.png)

为了方便读者查看，利用JSON Accelerator格式化一下

	{

  	"result" : {

    "sk" : {
      "humidity" : "58%",
      "wind_direction" : "西北风",
      "temp" : "26",
      "wind_strength" : "2级",
      "time" : "17:27"
    },

    "today" : {
      "temperature" : "22℃~27℃",
      "dressing_index" : "舒适",
      "dressing_advice" : "建议着长袖T恤、衬衫加单裤等服装。年老体弱者宜着针织长袖衬衫、马甲和长裤。",
      "uv_index" : "弱",
      "comfort_index" : "",
      "wind" : "东北风3-4 级",
      "weather" : "多云转阵雨",
      "city" : "苏州",
      "date_y" : "2015年09月24日",
      "week" : "星期四",
      "wash_index" : "不宜",
      "weather_id" : {
        "fa" : "01",
        "fb" : "03"
      },
      "travel_index" : "较适宜",
      "exercise_index" : "较适宜",
      "drying_index" : ""
    },

    "future" : [
      {
        "temperature" : "22℃~27℃",
        "weather_id" : {
          "fa" : "01",
          "fb" : "03"
        },
        "wind" : "东北风3-4 级",
        "week" : "星期四",
        "date" : "20150924",
        "weather" : "多云转阵雨"
      },
      {
        "temperature" : "21℃~26℃",
        "weather_id" : {
          "fa" : "03",
          "fb" : "01"
        },
        "wind" : "东北风3-4 级",
        "week" : "星期五",
        "date" : "20150925",
        "weather" : "阵雨转多云"
      },
      {
        "temperature" : "21℃~27℃",
        "weather_id" : {
          "fa" : "01",
          "fb" : "01"
        },
        "wind" : "东北风3-4 级",
        "week" : "星期六",
        "date" : "20150926",
        "weather" : "多云"
      },
      {
        "temperature" : "21℃~28℃",
        "weather_id" : {
          "fa" : "00",
          "fb" : "00"
        },
        "wind" : "东北风3-4 级",
        "week" : "星期日",
        "date" : "20150927",
        "weather" : "晴"
      },
      {
        "temperature" : "22℃~28℃",
        "weather_id" : {
          "fa" : "01",
          "fb" : "01"
        },
        "wind" : "东风3-4 级",
        "week" : "星期一",
        "date" : "20150928",
        "weather" : "多云"
      },
      {
        "temperature" : "22℃~28℃",
        "weather_id" : {
          "fa" : "01",
          "fb" : "03"
        },
        "wind" : "东风3-4 级",
        "week" : "星期二",
        "date" : "20150929",
        "weather" : "多云转阵雨"
      },
      {
        "temperature" : "20℃~25℃",
        "weather_id" : {
          "fa" : "04",
          "fb" : "04"
        },
        "wind" : "东南风4-5 级",
        "week" : "星期三",
        "date" : "20150930",
        "weather" : "雷阵雨"
      	}
    	]
	  },

	  "resultcode" : "200",

	  "reason" : "successed!",

	  "error_code" : 0
	}

接下来该轮到fastjson登场了，，先到<http://repo1.maven.org/maven2/com/alibaba/fastjson/>下载对应版本的jar包，或者到<https://github.com/alibaba/fastjson>下载源码，再导入到工程中。

	JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, JSON_URL, null,
                new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject jsonObject) {
                        try {
                            textView.setText(jsonObject.toString());

                            com.alibaba.fastjson.JSONObject jsonObject1 = JSON.parseObject(jsonObject.toString());

                            for (Map.Entry<String, Object> entry : jsonObject1.entrySet()) {
                                System.out.println(entry.getKey() + ":" + entry.getValue());
                            }

                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError volleyError) {
                Log.e(TAG, volleyError.getMessage());
            }
        });

![fastjson_03](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_03.png)

这样，简单的数据解析就完成了。

#### 何为JavaBean?
JavaBean是符合某种规范的java组件，也就是java类，它必须满足如下特性:    

* 有一个无参数的默认构造函数
* 有get和set方法

我们把公司员工抽象成一个类，然后利用fastjson来解析

	package com.djstava.fastjsondemo;

	/**
	 * 包名称: com.djstava.fastjsondemo
	 * 创建人: djstava
	 * 创建时间: 15/9/28 上午11:07
	 */
	public class Employee {

	    private int num;
	    private String name;
	    private int age;

	    public Employee(int num, String name, int age) {
	        this.num = num;
	        this.name = name;
	        this.age = age;
	    }

	    public void setNum(int num) {
	        this.num = num;
	    }

	    public void setName(String name) {
	        this.name = name;
	    }

	    public void setAge(int age) {
	        this.age = age;
	    }

	    public int getNum() {

	        return num;
	    }

	    public String getName() {
	        return name;
	    }

	    public int getAge() {
	        return age;
	    }

	    @Override
	    public String toString() {
	        return "Employee{" +
	                "num=" + num +
	                ", name='" + name + '\'' +
	                ", age=" + age +
	                '}';
	    }
	}

在Android Studio中，可以自动生成get、set、toString这些方法，Code->Generate，快捷键是Command + N。

![fastjson_04](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_04.gif)

	Employee employee  = new Employee(1,"djstava",20);
    Log.e(TAG,JSON.toJSONString(employee));

![fastjson_05](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_05.png)

可以看出输出的json格式与我们类中定义的属性的顺序不一致，其实没有关系，json都是通过key去获取其对应的value的。

如果仅仅是想解析JavaBean类中某几个字段时，可以使用SimplePropertyPreFilter这个类来实现，将需要解析的字段添加进来，如将Employee类中的"num"和"name"解析出来

	SimplePropertyPreFilter simplePropertyPreFilter = new SimplePropertyPreFilter(Employee.class,"num","name");
	Log.e(TAG,JSON.toJSONString(employee,simplePropertyPreFilter));

![fastjson_06](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/fastjson/fastjson_06.png)

### 源码下载

<http://download.csdn.net/detail/djstavav/9144789>

### 参考文献
1、<http://www.json.org/>    
2、<https://github.com/alibaba/fastjson/wiki>    
3、<https://www.juhe.cn/docs/api/id/39>
