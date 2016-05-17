# H5Demo
安卓webview html5 自动播放本地视频，网上视频，可以循环播放

##安卓webview html5 自动播放本地视频，网上视频，可以循环播放

有时候发现html5 视频的autoplay根本没实现，查看了下android 源码HTMLVideoElement.cpp也未见相应处理，
网上有资料显示，这可能关乎UI线程与后台线程的协调性，我看，以后的升级版本总会解决这个问题的。
html5标签属性不支持，那只有通过javascript来处理了


演示apk下载地址：http://download.csdn.net/detail/qiushi_1990/9513038
#下面是实现步骤和原理
一，我们在设置的WebViewClient中覆盖方法：
	不要忘记了webview.getSettings().settings.setJavaScriptEnabled(true)启用javascript,AndroidManifest加入INTERNET权限
	@Override
	public void onPageFinished( WebView view, String url) {
		view.loadUrl("javascript:try{autoplay();}catch(e){}");
	}
	
	实例代码
	 public class Html5VideoAutoPlay extends Activity {
		WebView webview = null;
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);

			setContentView(R.layout.html5video);

			webview = (WebView)findViewById(R.id.webview);
			webview.getSettings().setJavaScriptEnabled(true);
			webview.setWebViewClient(new WebViewClient(){
				/**
				 * 当前网页的链接仍在webView中跳转
				 */
				@Override
				public boolean shouldOverrideUrlLoading(WebView view, String url) {
					view.loadUrl(url);
					return true;
				}

				/**
				 * 处理ssl请求
				 */
				@Override
				public void onReceivedSslError(WebView view,
						SslErrorHandler handler, SslError error) {
					handler.proceed();
				}

				/**
				 * 页面载入完成回调
				 */
				@Override
				public void onPageFinished(WebView view, String url) {
					super.onPageFinished(view, url);
					view.loadUrl("javascript:try{autoplay();}catch(e){}");
				}
			});

			webview.setWebChromeClient(new WebChromeClient() {
				/**
				 * 显示自定义视图，无此方法视频不能播放
				 */
				@Override
				public void onShowCustomView(View view, CustomViewCallback callback) {
					super.onShowCustomView(view, callback);
				}
			});
			webview.loadUrl("file:///sdcard/html/video.html");
		}

		@Override
		protected void onPause() {
			if(null != webview) {
				webview.onPause();
			}
			super.onPause();
		}
    }
	
二，布局文件
	html5video.xml
	
		<?xml version="1.0" encoding="utf-8"?>
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
			android:layout_width="match_parent"
					android:layout_height="match_parent"
					android:orientation="vertical" >
		<WebView android:id="@+id/webview"
			android:layout_width="match_parent"
			android:layout_height="match_parent"/>
		</LinearLayout>
	
三，html文件（这里用的是html5的video标签来设置自动播放和循环播放）
	video.html

		<body>
		<video id="video" src="b.mp4" width="480" height="320" controls loop>
			don't support html5
		</video>
		</body>
		<script type="text/javascript">
			var video = document.getElementById("video");
			video.play();
		</script>
	
	上面的src可以引入本地视频b.mp4,
	也可以引入网上视频：http://2449.vod.myqcloud.com/2449_43b6f696980311e59ed467f22794e792.f20.mp4
	
个人觉得html5结合Android会让未来各种技术实现越来越简单。到我的个人博客查看更多h5与安卓的结合案例。
也可以关注我github里的其他开源项目。
个人博客：http://blog.csdn.net/qiushi_1990/article/details/51438198
github：https://github.com/qiushi123?tab=repositories

实例截图
![image](https://github.com/qiushi123/H5Demo/blob/master/images_apk/QQ%E6%88%AA%E5%9B%BE20160517175500.png?raw=true)




	
	
	
	
