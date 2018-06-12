本套通讯库是基于.Net FrameWork4.6.1开发的(.net版本在4.6以下https通讯会失败);
##主动请求通讯库使用方法
通讯库对外类以`GreenWhaleCoap`为主。完全限定名`GreenWhale.CoapCore.GreenWhaleCoap`,使用前请先配置好OceanConnect平台账号信息。
>注意：身份信息不初始化`CoapService`将不可使用。

示例代码如下：
```Csharp
 /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }
        GreenWhaleCoap green = GreenWhaleCoap.Instance;
        private void Button_Click_2(object sender, RoutedEventArgs e)
        {
            green.AuthenticationInfo.Name = AuthenticationInfo.APPID;
            green.AuthenticationInfo.Port = AuthenticationInfo.ServerPort;
            green.AuthenticationInfo.Pwd = AuthenticationInfo.APPSECRET;
            green.AuthenticationInfo.ServerPath = AuthenticationInfo.ServerAddress;
            green.AuthenticationInfo.SubscribePath = AuthenticationInfo.SubscribeBaseUri;
            MessageBox.Show("保存成功");
        }
    }
```
事件`SendData`会在HTTPS发送数据时调用。包含HTTPS的`HttpMethod`，`Headers`,`ContentType`,`Body`,`Uri`等信息。

事件`ReceiveData`会在HTTPS接收到服务端数据时会被调用，包含HTTP状态码`HttpStatusCode`,`HTML`纯文本，如果为JSON对象则可以看到JSON格式的对象

`CoapService`实现了接口`ICoapHelper`，西啊面介绍`ICoapHelper`，接口定义如下:
```Csharp
/// <summary>
    /// COAP帮助类
    /// </summary>
    public interface ICoapHelper
    {
        /// <summary>
        /// 撤销所有命令
        /// </summary>
        /// <param name="deviceId">设备ID号码</param>
        /// <returns></returns>
        Task<CancelCommandTaskResult> CancelAllCommands(string deviceId);
        /// <summary>
        /// 撤销设备单条命令
        /// </summary>
        /// <param name="deviceCommandId">命令ID号码</param>
        /// <returns></returns>
        Task<CommandResult> CancelCommandOfDevice(string deviceCommandId);
        /// <summary>
        ///发送命令到设备
        /// </summary>
        /// <param name="sendCommandBuilder"></param>
        /// <returns></returns>
        Task<CommandCreatResult> SendCommandToDevice(SendCommandBuilder sendCommandBuilder);
        /// <summary>
        /// 删除指定设备
        /// </summary>
        /// <param name="deviceid">设备ID号码</param>
        /// <returns></returns>
        Task<bool> DeleteDevice(string deviceid);
        /// <summary>
        /// 注册设备
        /// </summary>
        /// <param name="registerDeviceInfo"></param>
        /// <returns></returns>
        Task<RegisterDeviceResult> DevicesRegisterAsync(RegisterDeviceInfo registerDeviceInfo);
        /// <summary>
        /// 获取设备能力
        /// </summary>
        /// <param name="deviceId"></param>
        /// <param name="gatewayId"></param>
        /// <returns></returns>
        Task<DeviceCapabilityDTO> GetDeviceAbility(string deviceId, string gatewayId);
        /// <summary>
        /// 获取设备信息
        /// </summary>
        /// <param name="deviceid"></param>
        /// <returns></returns>
        Task<Device> GetDeviceInfo(string deviceid);
        /// <summary>
        /// 获取设备信息列表
        /// </summary>
        /// <param name="profileName">profile文件名称,这里指的是从服务器获取的Profile文件名称</param>
        /// <returns></returns>
        Task<DeviceInfoStatus[]> GetDeviceInfoList(string profileName);
        /// <summary>
        /// 获取设备列表
        /// </summary>
        /// <param name="nodeType">节点类型</param>
        /// <param name="status">状态</param>
        /// <param name="no">页码</param>
        /// <param name="size">单页大小</param>
        /// <param name="gatewayid">路由ID，为NULL即可</param>
        /// <param name="sortDirection">排序方向</param>
        /// <returns></returns>
        Task<Device[]> GetDeviceList(NodeType nodeType, Status status, int? no = 0, int? size = 1000, string gatewayid = null, SortDirection sortDirection = SortDirection.DESC);
        /// <summary>
        /// 获取设备状态
        /// </summary>
        /// <param name="deviceid">设备ID</param>
        /// <returns></returns>
        Task<DeviceStatus> GetDeviceStatus(string deviceid);
        /// <summary>
        /// 获取Profile文件列表
        /// </summary>
        /// <returns></returns>
        string[] GetProfileNames();
        /// <summary>
        /// 修改设备状态阈值信息
        /// </summary>
        /// <param name="abnormalTime">异常阈值信息</param>
        /// <param name="offlineTime">离线阈值信息</param>
        /// <returns></returns>
        Task<bool> ModifyApplicationTime(int? abnormalTime, int? offlineTime);
        /// <summary>
        /// 修改公司信息
        /// </summary>
        /// <param name="deviceid">设备ID</param>
        /// <param name="profileName">Profile文件名称</param>
        /// <param name="name">新的设备名称</param>
        /// <param name="location">设备位置</param>
        /// <returns></returns>
        Task<bool> ModifyComapnyName(string deviceid, string profileName, string name, string location);
        /// <summary>
        /// 修改设备信息
        /// </summary>
        /// <param name="deviceid">设备ID</param>
        /// <param name="deviceModifyInfo">设备修改信息</param>
        /// <returns></returns>
        Task<bool> ModifyDeviceInfomation(string deviceid, DeviceModifyInfo deviceModifyInfo);
        /// <summary>
        /// 读取撤销命令结果
        /// </summary>
        /// <param name="deviceId">设备ID</param>
        /// <returns></returns>
        Task<QueryCancelCommandTaskResult> QueryCancelCommandTaskResultAsync(string deviceId);
        /// <summary>
        /// 查询设备命令
        /// </summary>
        /// <param name="queryDeviceCommnadPar">设备命令查询</param>
        /// <returns></returns>
        Task<DeviceCommand> QueryCommandOfDeviceAsParam(QueryDeviceCommnadPar queryDeviceCommnadPar);
        /// <summary>
        /// 获取历史数据
        /// </summary>
        /// <param name="builder">历史数据查询构建器</param>
        /// <param name="GetDecodeResult">没有自己数据解析器第二个参数为NUll</param>
        /// <returns></returns>
        Task<History_Data[]> GetDeviceHistoryResolve(HistoryDataQuery_Builder builder, Resolover GetDecodeResult);
        /// <summary>
        /// 订阅消息
        /// </summary>
        /// <param name="notifyType">通知类型</param>
        /// <param name="callbackUrl">回调HTTPS地址</param>
        /// <returns></returns>
        Task<bool> SubscripMessage(NotifyType notifyType, Uri callbackUrl);
        /// <summary>
        /// 自动订阅消息（此方法需要预先设置服务器回调地址）
        /// </summary>
        /// <returns></returns>
        Task<bool[]> SubscripMessgaeAuto();
        /// <summary>
        /// 上传Profile文件
        /// </summary>
        /// <param name="profileName">Profile文件所在路径</param>
        /// <returns></returns>
        bool UploadProfile(string profileName);
    }
```



##HTTPS订阅回调
订阅功能以`GreenWhale.CoapCore.GreenWhaleCoap.HttpsListener`为主.
>使用前请务必先初始化PXF订阅证书文件，证书密码，以及监听的端口。
```Csharp
        /// <summary>
        /// PXF证书内容
        /// </summary>
        public byte[] RawData { get; set; }
        /// <summary>
        /// 证书密码
        /// </summary>
        public string Password { get; set; }
        /// <summary>
        /// Https本地监听端口
        /// </summary>
        public int Port { get;private set; }
```
调用
```Csharp 
public void Start(int port)
```
方法便可启动。
假设监听的端口为`6000`，则可使用浏览器访问`https://127.0.0.1:6000`进行访问。可依据弹出后的页面知晓SSL证书是否可信。Https服务是否启动。
接下来便是订阅处理的问题了。
请继承Router类。如下：
```Csharp
    /// 默认路由实现
    /// </summary>
    public class DefaultRouter:Router
    {
        public override void bindDevice(BindDevice content)
        {
            base.bindDevice(content);
        }
        public override void deviceAdded(DeviceAdded content)
        {
            base.deviceAdded(content);
        }
        public override void commandRsp(CommandRsp content)
        {
            base.commandRsp(content);
        }
        public override void deviceDataChanged(DeviceDataChanged content)
        {
            base.deviceDataChanged(content);
        }
        public override void deviceDatasChanged(DeviceDatasChanged content)
        {
            base.deviceDatasChanged(content);
        }
        public override void deviceDeleted(DeviceDeleted content)
        {
            base.deviceDeleted(content);
        }
        public override void deviceInfoChanged(DeviceInfoChanged content)
        {
            base.deviceInfoChanged(content);
        }
    }
```
实现后，将实现类新建对象赋值给`GreenWhale.CoapCore.GreenWhaleCoap.HttpsListener.Router`即可。Router父类，会根据不同的订阅自动调用对应方法。如果订阅发来的数据包错误，则会返回响应的错误信息。
