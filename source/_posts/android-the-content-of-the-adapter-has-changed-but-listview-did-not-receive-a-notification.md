---
title: >-
  Android解决：The content of the adapter has changed but ListView did not receive a notification
tags:
  - 多线程
categories:
  - android
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2015-11-19 11:33:00
desc: Android解决：The content of the adapter has changed but ListView did not receive a notification
keywords: The content of the adapter has changed but ListView did not receive a notification,adapter内容已改变，但listview未收到通知
---

项目中的错误，记录一下。

完整的错误信息如下：

```
The content of the adapter has changed but ListView did not receive a notification. Make sure the content of your adapter is not modified from a background thread, but only from the UI thread. [in ListView(2131492991, class android.widget.ListView) with Adapter(class com.impulsefitness.support.ui.WorkOrderDetailActivity$CustomFeedbackListViewAdapter)]
```

<!--more-->

大意是说adapter内容已改变，但listview未收到通知，上下文不同步了。

精简一下，我有两个ActivityA和ActivityB。首先打开ActivityA，显示一个列表（listView），然后点击一个按钮startActivityForResult打开了B，B中添加了信息提交后setResult到A。B负责添加数据，添加完数据回到A，通知A重新load一遍远端数据，没问题（不要问我为什么要重新load一遍）。

在Activity A中单独起了一个线程去处理任务（请求远端service获取数据），handler收到message之后启动这个线程t，然后Activity A又startActivityForResult了另一个Activity B（这时候ActivityA已经onPause了，但是线程依然在运行，而且还在handler的callback队列中），当Activity B处理完逻辑setResult给Activity A（并且把自己给finish掉），需要让ActivityA重新请求service数据，也就是说线程t要重新运行一次，这时候handler里面已经有线程t这个对象了，然后就导致了上述的错误（好像这么说很牵强啊），[参照CSDN牛人的文章](http://blog.csdn.net/garybook/article/details/7498342)，还是线程与上下文的事。

解决办法，在ActivityA的onPause将线程t从handler的callback中移除（然后handler再次收到同样的请求service的message时会重新将此线程t加入到callback队列中），如下：

```
@Override
    protected void onPause() {
        super.onPause();
        handler.removeCallbacks(tListFeedback);
    }
Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);

            switch (msg.what) {
                //页面加载，loading显示
                case 0x00:
                    pbFeedback.setVisibility(View.VISIBLE);
                    Message.obtain(this, 0x01, msg.obj).sendToTarget();
                    break;
                //请求service，用于获取反馈列表数据
                case 0x01:
                    tListFeedback = new ThreadListFeedback(msg.obj.toString());
                    ThreadPoolUtils.execute(tListFeedback);
                    break;
```

ActivityA的完整代码如下：

```
super.findViewById(R.id.tvContactAddress);
        tvComment2 = (TextView) super.findViewById(R.id.tvComment2);
        tvPartIdNm = (TextView) super.findViewById(R.id.tvPartIdNm);
        tvPartAmount = (TextView) super.findViewById(R.id.tvPartAmount);
        tvPartFee = (TextView) super.findViewById(R.id.tvPartFee);
        tvCustomerName = (TextView) super.findViewById(R.id.tvCustomerName);
        tvMobile = (TextView) super.findViewById(R.id.tvMobile);
        tvContactName = (TextView) super.findViewById(R.id.tvContactName);
        tvContactMobile = (TextView) super.findViewById(R.id.tvContactMobile);
        ivCall1 = (ImageView) super.findViewById(R.id.ivCall1);
        ivCall2 = (ImageView) super.findViewById(R.id.ivCall2);
        lvFeedback = (ListView) super.findViewById(R.id.lvFeedback);
        pbFeedback = (ProgressBar) super.findViewById(R.id.pbFeedback);

        customAdapter = new CustomFeedbackListViewAdapter(activity);
        lvFeedback.setEmptyView(super.findViewById(R.id.llListViewNoItemFeedbackDetail));
        lvFeedback.setAdapter(customAdapter);

        AddFeedbackListener feedbackListener = new AddFeedbackListener();
        btnAddFeedback.setOnClickListener(feedbackListener);

        FinishListener finishListener = new FinishListener();
        ivBack.setOnClickListener(finishListener);
        llIvBack.setOnClickListener(finishListener);

        tvTitle.setText(this.getResources().getText(R.string.activity_name_workorder_detail));

        Intent i = getIntent();
        Bundle b = i.getExtras();
        if (null == b) {
            tost(R.string.activity_workorder_detail_invalid_workorderno);
            this.finish();
        } else {
            String workOrderNo = b.getString("workorderno");
            if (null == workOrderNo) {
                tost(R.string.activity_workorder_detail_invalid_workorderno);
                this.finish();
            } else {

                Message.obtain(handler, 0x00, workOrderNo).sendToTarget();      //通知handler，开始load数据

                tvWorkOrderNo.setText(workOrderNo);
                tvServiceType.setText(b.getString("serviceType"));
                tvBookingTime.setText(b.getString("bookingTime"));
                tvContactAddress.setText(b.getString("constructAddress"));
                tvComment2.setText(b.getString("comment2"));
                tvPartIdNm.setText(b.getString("partId") + b.getString("partName"));
                tvPartAmount.setText(b.getString("partAmount") + "台");
                tvPartFee.setText(b.getString("partFee"));
                tvCustomerName.setText(b.getString("customerName"));
                tvMobile.setText(b.getString("mobile"));
                tvContactName.setText(b.getString("contactName"));
                tvContactMobile.setText(b.getString("contactMobile"));

                ivCall1.setOnClickListener(new CallListener(tvMobile.getText().toString()));
                ivCall2.setOnClickListener(new CallListener(tvContactMobile.getText().toString()));
            }
        }
    }

    /**
     * 自定义ListView Adapter
     */
    class CustomFeedbackListViewAdapter extends BaseAdapter {

        //对象所在的上下文Activity
        Activity activity;

        public CustomFeedbackListViewAdapter(Activity activity) {
            this.activity = activity;
        }

        @Override
        public int getCount() {
            return listViewData.size();
        }

        @Override
        public Object getItem(int position) {
            return listViewData.get(position);
        }

        @Override
        public long getItemId(int position) {
            return 0;
        }

        class ViewHolder {
            TextView tvFeedbackContent;
            ImageView ivArrowLaunch;
        }

        @Override
        public View getView(final int position, View convertView, ViewGroup parent) {

            ViewHolder holder;
            LayoutInflater inflater = activity.getLayoutInflater();

            if (convertView == null) {
                convertView = inflater.inflate(R.layout.listview_feedback_detail_item, null);
                holder = new ViewHolder();

            } else {
                holder = (ViewHolder) convertView.getTag();
                if (null == holder)
                    holder = new ViewHolder();
            }

            holder.tvFeedbackContent = (TextView) convertView.findViewById(R.id.tvFeedbackContentCombineStatement);
            holder.ivArrowLaunch = (ImageView) convertView.findViewById(R.id.ivFeedbackContentArrowLaunch);

            holder.ivArrowLaunch.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    tost(R.string.btn_txt_ok);
                }
            });

            HashMap<String, Object> map = listViewData.get(position);
            holder.tvFeedbackContent.setText(
                    map.get("employeeName").toString() + "在" + map.get("isrtDt") + "添加了反馈\"" + map.get("feedbackContent") + "\""
            );

            return convertView;
        }
    }

    ThreadListFeedback tListFeedback;
    /**
     * 消息处理handler
     */
    Handler handler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);

            switch (msg.what) {
                //页面加载，loading显示
                case 0x00:
                    pbFeedback.setVisibility(View.VISIBLE);
                    Message.obtain(this, 0x01, msg.obj).sendToTarget();
                    break;
                //请求service，用于获取反馈列表数据
                case 0x01:
                    tListFeedback = new ThreadListFeedback(msg.obj.toString());
                    ThreadPoolUtils.execute(tListFeedback);
                    break;
                //请求成功，已获取到数据
                case 0x02:
                    pbFeedback.setVisibility(View.GONE);
                    Bundle b = msg.getData();
                    if (null != b)
                        try {
                            SaxJson(b.get("remoteInfo"));
                        } catch (JSONException e) {
                            e.printStackTrace();
                            Message.obtain(this, -0x03, e.getMessage()).sendToTarget();
                        }
                    else
                        Message.obtain(this, -0x03, ":" + getString(activity, R.string.not_acquired_data)).sendToTarget();
                    break;
                //请求失败
                case -0x02:
                    pbFeedback.setVisibility(View.GONE);
                    String m = getString(activity, R.string.not_acquired_data) + msg.obj;
                    tost(m);
                    break;
                //解析json串异常
                case -0x03:
                    tost("parse error" + msg.obj);
                    break;
                //解析json正常，刷新UI
                case 0x04:
                    customAdapter.notifyDataSetChanged();
                    Message.obtain(this, 0x05).sendToTarget();
                    break;
                case 0x05:
                    setListViewHeight(lvFeedback);
                    break;
            }
        }
    };

    @Override
    protected void onPause() {
        super.onPause();
        handler.removeCallbacks(tListFeedback);
    }

    /**
     * 请求service获取反馈列表Runnable对象
     */
    class ThreadListFeedback extends Thread {

        String workOrderNo;

        public ThreadListFeedback(String workOrderNo) {
            this.workOrderNo = workOrderNo;
        }

        @Override
        public void run() {
            Map<String, Object> requestData = new HashMap<>();
            Bundle bundle = new Bundle();
            try {
                requestData.put("workOrderNo", workOrderNo);

                SoapInfo soapInfo = ServiceConstant.getWorkOrderFeedbackList();
                soapInfo.setRequestData(requestData);

                SoapService soapService = new SoapService(soapInfo);
                String remoteInfo = soapService.getRemoteInfo();
                bundle.putString("remoteInfo", remoteInfo);

                if (remoteInfo == "-1")
                    Message.obtain(handler, -0x02, getString(activity, R.string.activity_workorder_detail_invalid_workorderno)).sendToTarget();
                else {
                    Message m = Message.obtain(handler, 0x02);
                    m.setData(bundle);
                    m.sendToTarget();
                }

                Log.i(MainActivity.TAG, "remoteInfo:" + remoteInfo);

            } catch (Exception e) {
                e.printStackTrace();
                Message.obtain(handler, -0x02, e.getMessage()).sendToTarget();
            }
        }
    }

    /**
     * 解析service返回json串
     *
     * @param objJson
     */
    void SaxJson(Object objJson) throws JSONException {
        JSONArray jsonArray = new JSONArray(objJson.toString());

        for (int i = 0; i < jsonArray.length(); i++) {
            JSONObject jsonObject = (JSONObject) jsonArray.get(i);
            HashMap<String, Object> item = new HashMap<>();

            item.put("feedbackId", jsonObject.getString("feedbackId").trim());
            item.put("workOrderNo", jsonObject.getString("workOrderNo").trim());
            item.put("repairEmployeeId", jsonObject.getString("repairEmployeeId").trim());
            item.put("employeeName", jsonObject.getString("employeeName").trim());

            item.put("contactName", jsonObject.getString("contactName").trim());
            item.put("contactPhone", jsonObject.getString("contactPhone").trim());
            item.put("contactMobile", jsonObject.getString("contactMobile").trim());
            item.put("contactTime", jsonObject.getString("contactTime").trim());
            item.put("feedbackContent", jsonObject.getString("feedbackContent").trim());
            item.put("isrtDt", SimpleUtils.DateTimeUtils.parseTime("yyyy-MM-dd", jsonObject.getString("isrtDt").trim()));

            listViewData.add(item);
        }

        Message.obtain(handler, 0x04).sendToTarget();
    }

    /**
     * 添加反馈按钮事件监听器
     */
    class AddFeedbackListener implements View.OnClickListener {
        @Override
        public void onClick(View v) {

            Intent i = getIntent();
            i.putExtra("workorderno", tvWorkOrderNo.getText());
            i.setClass(WorkOrderDetailActivity.this, WorkOrderFeedbackActivity.class);
            startActivityForResult(i, 0x00);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        switch (requestCode) {
            case 0x00:
                //添加反馈完成，刷新数据
                if (null != data) {
                    boolean submitted = data.getBooleanExtra("submitted", false);
                    if (submitted)
                        Message.obtain(handler, 0x00, data.getStringExtra("workOrderNo")).sendToTarget();      //通知handler，开始load数据
                }
                break;
        }
    }

    /**
     * 关闭当前Activity单击事件监听器
     */
    class FinishListener implements View.OnClickListener {
        @Override
        public void onClick(View v) {
            WorkOrderDetailActivity.this.finish();
        }
    }

    /**
     * Call监听器
     */
    class CallListener implements View.OnClickListener {
        String telNo;

        public CallListener(String telNo) {
            this.telNo = telNo;
        }

        @Override
        public void onClick(View v) {
            Intent intent = new Intent(Intent.ACTION_DIAL, Uri.parse("tel://" + telNo));
            startActivity(intent);
        }
    }

    /**
     * 自定义actionBar布局文件
     * http://www.cnblogs.com/yc-755909659/p/4290784.html
     *
     * @param layoutId
     */

    void setActionBarLayout(int layoutId) {

        if (null != actionBar) {
            actionBar.setDisplayShowHomeEnabled(false);
            actionBar.setDisplayShowCustomEnabled(true);
            actionBar.setDisplayShowTitleEnabled(false);
            LayoutInflater inflator = (LayoutInflater) this.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            View v = inflator.inflate(layoutId, null);
            ActionBar.LayoutParams layout = new ActionBar.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
            actionBar.setCustomView(v, layout);
        }
    }
}
```

新解：

还是上面的错误信息，注意这句：

```
Make sure the content of your adapter is not modified from a background thread, but only from the UI thread.
```

确保adapter的内容不要在后台线程中修改，只能从UI线程中修改。而我的代码是直接message交给handler去处理的，难道这也算后台线程？于是想到了Runnable对象，它是依附于UI线程的，然后使用了handler.post(new Runnable())的方法，测试成功。