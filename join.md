# Join

前面两个方法，`zip()`和`merge()`方法作用在发射数据的范畴内，在决定如何操作值之前有些场景我们需要考虑时间的。RxJava的`join()`函数基于时间窗口将两个Observables发射的数据结合在一起。

![](chapter6_6.png)

为了正确的理解上一张图，我们解释下`join()`需要的参数：

* 第二个Observable和源Observable结合。
* `Func1`参数：在指定的由时间窗口定义时间间隔内，源Observable发射的数据和从第二个Observable发射的数据相互配合返回的Observable。
* `Func1`参数：在指定的由时间窗口定义时间间隔内，第二个Observable发射的数据和从源Observable发射的数据相互配合返回的Observable。
* `Func2`参数：定义已发射的数据如何与新发射的数据项相结合。
* 
如下练习的例子，我们可以修改`loadList()`函数像下面这样：
```java
private void loadList(List<AppInfo> apps) {
    mRecyclerView.setVisibility(View.VISIBLE);
    
    Observable<AppInfo> appsSequence =
    Observable.interval(1000, TimeUnit.MILLISECONDS)
                .map(position -> {
                    return apps.get(position.intValue());
                });
    Observable<Long> tictoc = Observable.interval(1000,TimeUnit.MILLISECONDS);
    
    appsSequence.join(
        tictoc, appInfo ->Observable.timer(2,TimeUnit.SECONDS),
        time - >Observable.timer(0, TimeUnit.SECONDS),this::updateTitle)
        .observeOn(AndroidSchedulers.mainThread())
        .take(10)
        .subscribe(new Observer<AppInfo>() {
            @Override
            public void onCompleted() {
                Toast.makeText(getActivity(), "Here is the list!", Toast.LENGTH_LONG).show();
            }
            
            @Override
            public void onError(Throwable e) {
                mSwipeRefreshLayout.setRefreshing(false); 
                Toast.makeText(getActivity(), "Something went wrong!", Toast.LENGTH_SHORT).show();
            }
            
            @Override
            public void onNext(AppInfoappInfo) {
                if (mSwipeRefreshLayout.isRefreshing()) {
                    mSwipeRefreshLayout.setRefreshing(false);
                } 
                mAddedApps.add(appInfo);
                int position = mAddedApps.size() - 1;
                mAdapter.addApplication(position, appInfo);
                mRecyclerView.smoothScrollToPosition(position);
            } 
        });
}
```
