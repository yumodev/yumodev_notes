

#需求场景
      通过WebView控件显示一个网页的时候，需要显示一个加载进度反馈给用户。下面是该功能实现的核心代码

# 核心代码

```
LinearLayout rootView = new LinearLayout(getContext());
rootView.setOrientation(LinearLayout.VERTICAL);

//创建一个水平进度条，并添加到富容器中。
final ProgressBar progressBar = new ProgressBar(getContext(),null, android.R.attr.progressBarStyleHorizontal);
progressBar.setLayoutParams(new LinearLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, 20));
rootView.addView(progressBar);

//新建一个WebView,加入父容器中，并打开http://www.baidu.com.
WebView webView = new WebView(getContext());
webView.getSettings().setJavaScriptEnabled(true);
webView.loadUrl("http://www.baidu.com");
rootView.addView(webView, 1);

webView.setWebViewClient(new WebViewClient() {
  @Override
  public boolean shouldOverrideUrlLoading(WebView view, String url) {
      view.loadUrl(url);
      return true;
  }

  @Override
  public void onPageStarted(WebView view, String url, Bitmap favicon) {
      super.onPageStarted(view, url, favicon);
      progressBar.setVisibility(View.VISIBLE);
  }
});

webView.setWebChromeClient(new WebChromeClient() {

  @Override
  public void onProgressChanged(WebView view, int newProgress) {
      super.onProgressChanged(view, newProgress);
      progressBar.setProgress(newProgress);

      if (newProgress == 100) {
          progressBar.setVisibility(View.GONE);
      }
  }
});
```

# 主要功能点

	WebChromeClient 类中有一个OnProgressChanged()方法。当网页加载的时候，对不断的调用该方法，并把具体的进度值传递过来。然后就可以方便的调整进度条的显示。

