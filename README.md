# Utils
##超级工具包

版本：2.3.5<br>
作者：西门提督<br>
日期：2016-12-15

##Utils超级工具包用法如下：

###1.AndroidManifest.xml添加权限：
    <!-- 往sdcard中写入数据的权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 在sdcard中创建/删除文件的权限 -->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
    <!-- 读取sdcard权限 -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <!-- 如果需要做网络监听事件则需要添加权限： -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />

    <!-- 添加广播 -->
    <receiver android:name="com.cmonbaby.utils.net.NetStateReceiver">
        <intent-filter>
            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
            <action android:name="cmonbaby.net.conn.CONNECTIVITY_CHANGE" />
        </intent-filter>
    </receiver>

###2.BaseApplication初始化操作：
public class BaseApplication extends Application {
    // 网络是否已打开已获得
    protected Boolean networkAvailable = false;
        // 网络状态监听
        protected NetChangeObserver netObserver;

        @Override
        public void onCreate() {
            super.onCreate();
            UtilsManager.Opration.setDebug(true); // 正式签名打包，建议关闭Log日志
            UtilsManager.Opration.setToastShow(true);
            UtilsManager.Opration.setAppName("utils"); // 必填
            UtilsManager.Opration.setPackageName(getPackageName()); // 必填
            UtilsManager.Opration.setLogTag("utils >>> "); // 可不填
            UtilsManager.Opration.setPreferencesFileName("com.utils"); // 可不填
            UtilsManager.Opration.setVersionCode(String.valueOf(PackageUtils.getAppVersionCode(this))); // 可不填
            // UtilsManager.Opration.setFileName(this, "files", "crash"); // 任意类新建目录：Android/data/包名/一级/二级/

            // App异常崩溃处理器
            CrashHandler crashHandler = CrashHandler.getInstance();
            crashHandler.init(this, "服务端上传错误日志URL");

            initNetWork();
        }

        private void initNetWork() {
            netObserver = new NetChangeObserver() {
                @Override
                public void onConnect(NetType type) {
                    // 网络连接连接时调用
                    networkAvailable = true;
                    // 逻辑代码省略一百行
                }

                @Override
                public void onDisConnect() {
                    // 当前没有网络连接
                    networkAvailable = false;
                    // 逻辑代码省略一百行
                }
            };
            NetStateReceiver.registerObserver(netObserver);
        }

        // 获取当前网络状态，true为网络连接成功，否则网络连接失败
        protected Boolean isNetworkAvailable() {
            return networkAvailable;
        }
    }

###详细用法：（更新中）
####意图：
    IntentUtils.builder(this)
            .targetClass(TestActivity.class) // 目标跳转
            .booleanParams("isExam", true) // boolean参数
            .intParams("examCount", 10) // int参数
            .longParams("examDate", System.currentTimeMillis()) // long参数
            .stringParams("account", "Simon") // String参数
            .serializableObj("user", user) // 序列化对象参数
            .parcelableObj("version", version) // Parcelable对象参数
            .isForResult(true) // 是否跳转回调
            .requestCode(Cons.REQUESTCODE1) // 跳转时匹配码
            .isResult(false) // 是否回调
            .resultCode(Cons.RESULTCODE1) // 回调时匹配码
            .isFinish(false) // 是否销毁
            .jump();

####吐司：
    ToastUtils.builder(this)
            .viewLayout(R.layout.nursing_toast) // 自定义布局
            .message(a + " / " + b + " / " + c + " / " + d ) // 吐司内容
            .textViewId(R.id.tv_toast) // 布局id
            .textSize(20) // 字体大小
            .textColor(R.color.orange) // 字体颜色
            .gravity(Gravity.CENTER) // 吐司位置，默认中间
            .duration(1) // 吐司时间Long，默认0，Short
            .listener(new ToastUtils.ViewListener() { // 可不监听
                @Override
                public void listener(View view) {
                    TextView tv = (TextView) view.findViewById(R.id.tv_toast2);
                    if (tv != null) tv.setText("test >>> ");
                }
            })
            .show();
