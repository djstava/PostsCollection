libcurl是一个免费的、易于使用的的基于URL的传输库，支持多种协议，如http、https、ftp等等。libcurl常被来用下载、上传文件。


libcurl下载地址: <http://curl.haxx.se/download.html>    
openssl下载地址: <https://www.openssl.org/source/>

#### 编译openssl
	tar xvf openssl-1.0.1e.tar.bz2
	cd openssl-1.0.1e
    mkdir djstava
    ./config no-asm --prefix=/home/djstava/Downloads/openssl-1.0.1e/djstava --openssldir=/home/djstava/Downloads/openssl-1.0.1e/djstava
    make
    make install

    vi djstava/lib/pkgconfig/openssl.pc
        修改为 Libs: -L${libdir} -lssl -lcrypto -ldl

#### 编译libcurl
	tar xvf curl-7.31.0.tar.bz2
	cd curl-7.31.0
    mkdir djstava
    export PKG_CONFIG_PATH=/home/djstava/Downloads/openssl-1.0.1e/djstava/lib/pkgconfig
    export LDFLAGS=-L/home/djstava/Downloads/openssl-1.0.1e/djstava/lib/
    ./configure --prefix=/home/djstava/Downloads/curl-7.31.0/djstava --with-ssl=/home/djstava/Downloads/openssl-1.0.1e/djstava/
    make
    make install

#### 使用libcurl实现http的文件下载

###### web server
	sudo apt-get install nginx    

服务器搭建完毕，将提供下载的文件(如test.txt)拷贝到nginx的根目录

##### client

	#define CURLOPT_CONNECTTIMEOUT 30
	#define CURLOPT_TIMEOUT 120

	int main()
	{
		char img_url[128] = "http://192.168.1.100/test.txt";
		FILE *fp = NULL;
		CURL *curl = NULL;
		int curlRet = 0;

		fp = fopen("download.file","wb");
		if(fp == NULL)
		{
			printf("open file error.\n");
		}

		curl = curl_easy_init();
		if(!curl)
			printf("Curl init failed.\n");

		curl_easy_setopt(curl, CURLOPT_URL, img_url);
		curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, &process_data);
		curl_easy_setopt(curl, CURLOPT_WRITEDATA, fp);
		curl_easy_setopt(curl, CURLOPT_NOPROGRESS, 0);
		curl_easy_setopt(curl, CURLOPT_PROGRESSFUNCTION, getProgressValue);
		curl_easy_setopt(curl, CURLOPT_PROGRESSDATA, curl);
		curl_easy_setopt(curl, CURLOPT_CONNECTTIMEOUT, CONNECT_TIMEOUT);
		curl_easy_setopt(curl, CURLOPT_TIMEOUT, DOWNLOAD_TIMEOUT);
		curl_easy_setopt(curl, CURLOPT_VERBOSE,1);
		curlRet = curl_easy_perform(curl);

		if(CURLE_OK == curlRet)
		{
			printf("Download finished.\n");
		}

		fclose(fp);
		curl_easy_cleanup(curl);
	}

	/**
 	* @brief Write the receiving data to a local file
 	* @param[in] void* buffer
 	* @param[in] size_t size
 	* @param[in] size_t nmemb
 	* @param[in] void* user_p
 	* @return size_t
 	*/
	size_t process_data(void *buffer, size_t size, size_t nmemb, void *user_p)
	{
		static double totalfsize = 0;
		FILE *fp = (FILE *)user_p;
		size_t return_size = fwrite(buffer, size, nmemb, fp);
		totalfsize += return_size;
		//printf("download:%f\n",totalfsize);
		return return_size;
	}

    /**
 	* @brief Calculate the percent of the data
	* @param[in] char* flag
 	* @param[in] double dt
 	* @param[in] double dn
 	* @param[in] double ult
 	* @param[in] double uln
 	* @return int
 	*/
	int getProgressValue(const char* flag, double dt, double dn, double ult, double uln)
	{  
		int progress = (dn*100.0)/dt;

		printf("progress = %d\n",progress);
		return 0;
	}  

基于https协议的文件下载也可以如上实现，只是在编译libcurl时加上openssl的编译选项。
