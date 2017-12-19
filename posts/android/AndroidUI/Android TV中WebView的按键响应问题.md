### 软硬件环境

* Macbook Pro MGX 72
* Android studio 1.0.2
* BesTV小红

###问题场景
当布局中是一个WebView时，打开页面后，焦点就移到了WebView上，默认情况下，此时遥控器只响应返回键，如果需要响应其它键值(比如菜单键)，这时该怎么做呢？本文就来解答这个问题。

###应对方法
一般的键值处理是放在onKeyDown(int keyCode,KeyEvent keyevent)里处理的，不过在上述场景中，onKeyDown方法中接收不到菜单键，如果想让其也能够接收到，也不难，往下看。

	webView = (WebView)findViewById(R.id.webView);

	WebSettings webSettings = webView.getSettings();
	webSettings.setJavaScriptEnabled(true);
	webSettings.setUseWideViewPort(true);
	webSettings.setLoadWithOverviewMode(true);
	
	webView.setWebViewClient(new WebViewClient(){
	        @Override
	        public boolean shouldOverrideUrlLoading(WebView view, String url) {
	            view.loadUrl(url);
	            //return super.shouldOverrideUrlLoading(view, url);
	            return true;
	        }
	
	        @Override
	        public boolean shouldOverrideKeyEvent(WebView webView,KeyEvent keyEvent) {
	            //让onKeyDown来处理KeyEvent.KEYCODE_STAR,此处对应小红遥控器菜单键
	            if (keyEvent.getKeyCode() == KeyEvent.KEYCODE_STAR) {
	                return true;
	            } else {
	                return false;
	            }
	        }
	    }