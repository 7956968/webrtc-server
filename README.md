# webrtc-server
* webrtc流媒体服务器  
* 基于licode的erizo改造  
* licode的底层stun协议用库libnice交互，为了便于理解webrtc交互过程，更改底层stun实现  
* erizo/MyIce基于net网络库实现udp通信，提取mediasoup的stun协议解析实现stun交互  
# 依赖库
```  
sudo apt-get install liblog4cxx-dev  
sudo apt install libavcodec-dev libavdevice-dev libavfilter-dev libavformat-dev libavresample-dev libavutil-dev libpostproc-dev   libswresample-dev libswscale-dev  
sudo apt-get install libboost-all-dev  
```  

# 目录说明
* net  一个类似muduo网络库  
* rapidjson 腾讯json库，解析json用  
* thirdparty 第三方库，srtp2和openssl版本1.02的头文件和静态库  
* webrtchtml webrtc视频播放的网页 
* erizo webrtc协议，包括stun，dtls，srtp，rtp，rtcp  
* webrtc 提取webrtc带宽预测检测模块   
* websocketpp websocket网络库，依赖boost  
* example ffmpeg编码带有日期时间的h264流，websocket信令服务器交换sdp  
* ZLMediaKit [ZLMediaKit github地址](https://github.com/xiongziliang/ZLMediaKit "ZLMediaKit")  
# 使用说明
ubuntu18.04安装依赖库  
```  
mkdir build  
cd build   
cmake ..  
make  
```  
运行程序   
修改webrtchtml/js/main.js下websocket的ip地址   
打开webrtchtml/index.html 播放视频 

# 目录介绍

# 原理说明
* net目录类似muduo网络库，主要用poller和channal封装一个udpsocket；  
* 基于boost建立一个websocket信令服务器，交换webrtc所需要的sdp信息。  
* 网页上打开一个websocket连接，服务器建立一个WebRtcTransport，底层是一个udpsocket。  
* WebRtcTransport生成sdp信息，通过websocket传到前端。    
* sdp信息包括媒体信息如编码格式、ssrc等，stun协议需要的ice-ufrag、ice-pwd、candidate,dtls需要的fingerprint。  
* 前端通过candidate获取ip地址和端口号，通过udp协议连接到服务器的。  
* 服务器收到udp报文，先后通过类UdpSocket接收报文；StunPacket和IceServer解析stun协议；stun协议交互成功后  
通过DtlsTransport进行dtls握手；交换密钥后就可以初始化SrtpChannel。  
* FFmpeg生成h264流通过ZLMediaKit开源项目的MultiMediaSourceMuxer生成rtp流，通过SrtpChannel加密，通过udpsocket发送，前端就可以看到视频。  

