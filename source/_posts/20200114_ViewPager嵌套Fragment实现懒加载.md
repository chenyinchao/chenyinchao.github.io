---
title: ViewPager嵌套Fragment实现懒加载
date: 2020-01-14 17:36:11
---

#### 背景
使用QMUITabSegment实现ViewPager嵌套Fragment

#### 存在的问题
Fragment依附的Activity生命周期只走一遍，里面的fragment不走生命周期，导致两个fragment一次性都加载出来了

#### 实现的效果
两个Fragment独立加载数据，不是一次同时都加载

#### 解决办法
```java
    private boolean myIsVisibleToUser;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View chatView = inflater.inflate(R.layout.activity_tab_detail, container, false);
        ExitApplication.getInstance().addActivity(getActivity());
        initView(chatView);
        return chatView;
    }

    /**
     *  ViewPager嵌套fragment导致两个fragment生命周期只走一次，通过实现懒加载，每切换一次fragment即执行
     *  一次setUserVisibleHint方法，因为setUserVisibleHint执行比onAttach还要前，为避免getActivity空指针，
     *  同时在onActivityCreated里初始化一下
     * @param isVisibleToUser
     */
    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        super.setUserVisibleHint(isVisibleToUser);
        Log.i("CHENYINCHAO", "setUserVisibleHint");
        myIsVisibleToUser = isVisibleToUser;
        if (isVisibleToUser && getActivity() != null) {
            SelectNoFinishTask("", i, rows);
        }
    }

    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        Log.i("CHENYINCHAO", "onActivityCreated");
        setUserVisibleHint(myIsVisibleToUser);
    }
```

参考：
setuservisiblehint如何避免context空指针
https://stackoverflow.com/questions/39105725/null-context-in-setuservisiblehint