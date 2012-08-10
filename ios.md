APNS

http://developer.apple.com/library/mac/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9

provider -- (device token, certificate) -> APNS -> device -> app

certificate中有bundle id来标识应用

发送的通知可包括几部分
	message
	badge
	sound

app注册通知服务时会由ios向APNS获取device token, 但获取到的device token始终不变吗？

device证书中有device id, device token中也包含device id

APNS向device发送消息是根据包含在device token中的device id

用户登陆后，发送 
