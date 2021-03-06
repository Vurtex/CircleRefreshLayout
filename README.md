This is a project with custom pull-to-refresh layout which contains a interesting animation. And the animation is inspired by <https://dribbble.com/shots/1797373-Pull-Down-To-Refresh> made by Ramotion.  

###Demo###


![](gif/circlerefresh.gif)  

AS

添加JitPack仓库

``` java
allprojects {
        repositories {
            ...
            maven { url 'https://jitpack.io' }
        }
    }
```
添加依赖

``` java
dependencies {
            compile 'com.github.Vurtex:CircleRefreshLayout:0.0.1'
	}
```

###Usage###


``` xml
<com.tuesda.walker.circlerefresh.CircleRefreshLayout
        xmlns:app="http://schemas.android.com/apk/res-auto"
        app:AniBackColor="#ff8b90af"
        app:AniForeColor="#ffffffff"
        app:CircleSmaller="6"
        android:id="@+id/refresh_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <ListView
            android:background="#ffffffff"
            android:id="@+id/list"
            android:layout_width="match_parent"
            android:layout_height="match_parent"></ListView>
    </com.tuesda.walker.circlerefresh.CircleRefreshLayout>
```  

Call back when refresh starts and complete:  

``` java
mRefreshLayout.setOnRefreshListener(
                new CircleRefreshLayout.OnCircleRefreshListener() {
            @Override
            public void refreshing() {
                // do something when refresh starts
            }

            @Override
            public void completeRefresh() {
                // do something when refresh complete
            }
        });
```

when refreshing is done(for example, the image loading completes), you can invoke:  

``` java
mRefreshLayout.finishRefreshing();
```

修改在Fragment中使用mRefreLayout.finishRefreshing()不返回列表顶部的问题。

在CircleRefreshLayout中添加：
``` java
public void finishRefreshing() {
        if (onCircleRefreshListener != null) {
            onCircleRefreshListener.completeRefresh();
        }
        mIsRefreshing = false;
        mHeader.setRefreshing(false);
        back();
    }
    private void back() {
        float height = mChildView.getTranslationY();
        ValueAnimator backTopAni = ValueAnimator.ofFloat(height, 0);
        backTopAni.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                float val = (float) animation.getAnimatedValue();
                val = decelerateInterpolator.getInterpolation(val / mHeaderHeight) * val;
                if (mChildView != null) {
                    mChildView.setTranslationY(val);
                }
                mHeader.getLayoutParams().height = (int) val;
                mHeader.requestLayout();
            }
        });
        backTopAni.setDuration((long) (height * BACK_TOP_DUR / mHeaderHeight));
        backTopAni.start();
    }
```
