# bug

1. 今天安卓开发时，遇到一个bug，记录一下。使用androidx的版本，在textview中绑定一个onclick方法，但是在fragment中的这个onclick方法中，d

```java
	/**
     * 日历选择器
     *
     * @param view  要显示时间的控件
     * @param title 日历title
     * @return
     */
    public TimePickerView intTimePicker(final View view, String title) {
        //时间选择器
        TimePickerView pvTime = new TimePickerView(_mActivity, TimePickerView.Type.ALL);
        //控制时间范围
//      Calendar calendar = Calendar.getInstance();
//      pvTime.setRange(calendar.get(Calendar.YEAR) - 20, calendar.get(Calendar.YEAR));//要在setTime 之前才有效果哦
        pvTime.setTime(new Date());
        pvTime.setCyclic(false);
        pvTime.setTitle(title);
        pvTime.setCancelable(true);
        //时间选择后回调
        pvTime.setOnTimeSelectListener(new TimePickerView.OnTimeSelectListener() {
            @Override
            public void onTimeSelect(Date date) {
                ((TextView) view).setText(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date));
            }
        });
        //选项选择器
        // pvOptions = new OptionsPickerView(this);
        return pvTime;
    }


intTimePicker(cruxAlarmHdStart,"开始时间").show();

```

