### 软硬件环境
* Macbook Pro MGX 72
* Android Studio 1.3.1
* 红米Note增强版

### 准备工作
* 上高德官网LBS开放平台注册帐户    
* 下载SDK和Demo程序  
	地址: <http://lbs.amap.com/api/android-navi-sdk/down/>  
* 给自己的应用申请Key    
	这个具体步骤高德的官网上都有详细的说明及操作步骤，就不多说了，照着做就行了

	![djamap_01.png](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djamap/djamap_01.png)

### Android Studio运行高德导航Demo
#### 修改AndroidManifest.xml
	<meta-data
        android:name="com.amap.api.v2.apikey"
        android:value="4e8ee81efa8c19c8d9e0b55761f6962d" />

	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
	<uses-permission android:name="android.permission.READ_PHONE_STATE" />
	<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
	<uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
	<uses-permission android:name="android.permission.WAKE_LOCK" />
	<uses-permission android:name="android.permission.WRITE_SETTINGS" />
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

### 常见错误及解决办法
* android SDK V4和V7冲突？

![djamap_02.png](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djamap/djamap_02.png)
	首先在app下的build.gradle文件中删除provided files('libs/android-support-v4.jar')或者通过File菜单下的Project Structure中的app模块中的Dependencies选项删除。然后在工程libs目录下删除android-support-v4.jar文件。

* 工程运行后报TTSController初始化失败？
	我的做法是把libs中的so库全部copy出来，放在jniLibs文件夹中，jniLibs是自己创建的，跟libs在同一层目录下(其它目录也可以)，然后在build.gradle中声明下，以下这段语句放在android标签下即可

		sourceSets {
        main {
            jniLibs.srcDirs = ['jniLibs']
        	}
    	}

大功告成，来张图秀一下

![djamap_03.png](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/android/djamap/djamap_03.png)

### 进行一次定位

	   private LocationManagerProxy mLocationManager;
      private AMapLocationListener mLocationListener = new AMapLocationListener() {

	   @Override
	   public void onStatusChanged(String provider, int status, Bundle extras) {
	       // TODO Auto-generated method stub

	   }

	   @Override
	   public void onProviderEnabled(String provider) {
	       // TODO Auto-generated method stub

	   }

	   @Override
	   public void onProviderDisabled(String provider) {
	       // TODO Auto-generated method stub

	   }

	   @Override
	   public void onLocationChanged(Location location) {
	       // TODO Auto-generated method stub

	   }

	   @Override
	   public void onLocationChanged(AMapLocation location) {
	       if (location != null && location.getAMapException().getErrorCode() == 0) {

	           Log.e(TAG, "GPS: " + location.getLatitude() + "," + location.getLongitude());

	           LatLng latLng = new LatLng(mStartPoint.getLatitude(), mStartPoint.getLongitude());

	           MarkerOptions markerOptions = new MarkerOptions();
	           markerOptions.position(latLng);
	           markerOptions.title("您当前的位置");

	           mAmap.addMarker(markerOptions);
	           mAmap.moveCamera(CameraUpdateFactory.newLatLngZoom(latLng, 12));

	       } else {
	           showToast("定位出现异常,请检查网络连接.");
	           mGPSProgressDialog.dismiss();

	           //以上海市人民政府为中心显示定位时出错的初始地图
	           LatLng latLng = new LatLng(31.230429, 121.473709);
	           mAmap.moveCamera(CameraUpdateFactory.newLatLngZoom(latLng, 12));
	       }
	   }
	};


	mLocationManager = LocationManagerProxy.getInstance(this);
	mLocationManger.requestLocationData(LocationProviderProxy.AMapNetwork, -1, 15, mLocationListener);


### 兴趣点(Point Of Interest)查询
应用场景一般是这样的: 在输入框中输入关键字如"东方明珠",系统则提供所有跟"东方明珠"相关的地址信息供用户选择。以下代码是在一个输入框中输入一字符串，经过查询POI Search将相关结果返回，以listview的形式显示出来，经用户点击后将具体的信息写入到输入框中。原来项目需要，这里还记录了对应地址的经纬度数据。

						//输入框是一个EditText
	                mEndPointText.addTextChangedListener(new TextWatcher() {
                    @Override
                    public void beforeTextChanged(CharSequence s, int start, int count, int after) {

                    }

                    @Override
                    public void onTextChanged(CharSequence s, int start, int before, int count) {
                        String newText = s.toString().trim();

                        PoiSearch.Query query = new PoiSearch.Query(newText, "", "021");
                        query.setPageSize(10);
                        query.setPageNum(1);
                        PoiSearch poiSearch = new PoiSearch(NaviStartActivity.this, query);
                        poiSearch.setOnPoiSearchListener(new PoiSearch.OnPoiSearchListener() {
                            @Override
                            public void onPoiSearched(PoiResult poiResult, int i) {
                                if (i == 0) {
                                    for (int j = 0; j < poiResult.getPois().size(); j++) {
                                        if (!poiResult.getPois().get(j).getSnippet().isEmpty()) {
                                            poiAddressList.add(poiResult.getPois().get(j).getSnippet());
                                            poiLatLonList.add(poiResult.getPois().get(j).getLatLonPoint());
                                        }
                                    }

                                    poiAddressArray = new String[poiAddressList.size()];
                                    poiAddressArray = poiAddressList.toArray(poiAddressArray);

                                    poiLatLngArray = new LatLonPoint[poiLatLonList.size()];
                                    poiLatLngArray = poiLatLonList.toArray(poiLatLngArray);

                                    adapterTextview_poi_result = new AdapterTextview(NaviStartActivity.this, poiAddressArray);
                                    listView_poi_result.setAdapter(adapterTextview_poi_result);
                                    adapterTextview_poi_result.notifyDataSetChanged();

                                    poiAddressList.clear();
                                    poiLatLonList.clear();

                                }
                            }

                            @Override
                            public void onPoiItemDetailSearched(PoiItemDetail poiItemDetail, int i) {

                            }
                        });

                        listView_poi_result.setOnItemClickListener(new OnItemClickListener() {
                            @Override
                            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                                mEndPoints.clear();
                                mEndPointText.setText(poiAddressArray[position]);
                                mEndPoint = new NaviLatLng(poiLatLngArray[position].getLatitude(), poiLatLngArray[position].getLongitude());
                                mEndPoints.add(mEndPoint);
                            }
                        });

                        poiSearch.searchPOIAsyn();
                    }

                    @Override
                    public void afterTextChanged(Editable s) {

                    }
                });


### 地理编码
地理编码，通俗来讲就是将具体的地址(如xx区xx路)转换成经纬度数据，来看代码

	GeocodeSearch geocodeSearch = new GeocodeSearch(this);
        geocodeSearch.setOnGeocodeSearchListener(new GeocodeSearch.OnGeocodeSearchListener() {
            @Override
            public void onRegeocodeSearched(RegeocodeResult regeocodeResult, int i) {
                //逆地理编码，经纬度转换成地址
            }

            @Override
            public void onGeocodeSearched(GeocodeResult geocodeResult, int i) {
                //地理编码，地址转换成经纬度
                if (i == 0) {
                    if (geocodeResult != null && geocodeResult.getGeocodeAddressList() != null && geocodeResult.getGeocodeAddressList().size() > 0) {
                        GeocodeAddress geocodeAddress = geocodeResult.getGeocodeAddressList().get(0);
                        Log.e(TAG, "destination position: " + geocodeAddress.getLatLonPoint());

                    }
                } else {
                    Toast.makeText(NaviStartActivity.this, "该地址不存在!", Toast.LENGTH_SHORT).show();
                }

            }
        });

        //以上海地区地址的查询为例,城市代码可以查看高德的文档
        GeocodeQuery geocodeQuery = new GeocodeQuery(address, "021");
        geocodeSearch.getFromLocationNameAsyn(geocodeQuery);


### 完整导航Demo的下载地址
<http://download.csdn.net/detail/djstavav/9016909>

### Github地址
<https://github.com/djstava/DJAmap>
