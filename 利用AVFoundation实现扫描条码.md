title: 利用AVFoundation实现扫描条码
date: 2015-06-16 17:39:05
tags: iOS
---

iOS7中添加了一组原生支持扫描的组件，通过AVCaptureMetaDataOutput可以扫描各式各样的QR码，条形码，你所需要做的就是写好UI，并将它设置为AVCaptureSession的输出，再实现相应的协议即可。而且，这玩意可以跟Passbook相结合，有许多潜力可以实现，官方也非常推荐大家使用这个API。

> import AVFoundation

> 实现 AVCaptureMetadataOutputObjectsDelegate协议


要实现这个扫描器，首先需要导入AVFoundation框架，这是一个专门处理音频，视频的框架。

创建一个AV设备，使用AVMediaTypeVideo常量

	let captureDevice = AVCaptureDevice.defaultDeviceWithMediaType(AVMediaTypeVideo)
	
然后建立一个输入

	let input:AnyObject! = AVCaptureDeviceInput.deviceInputWithDevice(captureDevice, error: &error)
	

现在，我们来看一看，最关键的部分，创建输出会话，不过在此之前，我们需要先使用AVCaptureSession创建一个会话，将输入端添加到会话中。

	self.captureSession = AVCaptureSession()
	self.captureSession?.addInput(input as! AVCaptureInput)

接着，我们开始使用AVCaptureMetadataOutput创建一个输出端

	let captureMetadataOutput = AVCaptureMetadataOutput()
	captureMetadataOutput.setMetadataObjectsDelegate(self, queue: dispatch_get_main_queue())
    captureMetadataOutput.metadataObjectTypes = [AVMetadataObjectTypeEAN13Code,AVMetadataObjectTypeEAN8Code, AVMetadataObjectTypeCode128Code,AVMetadataObjectTypeQRCode]
    
指定要实现的扫描常量，添加一个协议，GCD可以使用在主线程中，并把它添加到会话中。

	self.captureSession?.addOutput(captureMetadataOutput)
	
然后我们还需要使用AVCaptureVideoPreviewLayer创建一个画布，用来显示扫描的展示区域，并添加到当前视图的layer中。

	self.videoPreviewLayer = AVCaptureVideoPreviewLayer(session: self.captureSession)
    self.videoPreviewLayer?.videoGravity = AVLayerVideoGravityResizeAspect
    self.videoPreviewLayer?.frame = self.view.bounds
    self.view.layer.addSublayer(self.videoPreviewLayer)

最后启动这个会话

	self.captureSession?.startRunning()
	
到这里，整个扫描条码的程序就已经准备就绪了，但是如果你还需要获取扫描相应的结果，那么需要实现它的协议，并实现func captureOutput(captureOutput: AVCaptureOutput!, didOutputMetadataObjects metadataObjects: [AnyObject]!, fromConnection connection: AVCaptureConnection!) {} 方法。

metadataObjects是一个AnyObject的数组，在这里，还需要进步的判断，获取第一个元素转化为AVMetadataMachineReadableCodeObject，通过type，可以判断扫描的类型，是二维码，还是条形码，还是其他，而值，则是在AVMetadataMachineReadableCodeObject的stringValue属性。

	let metadata = metadataObjects[0] as! AVMetadataMachineReadableCodeObject
	//metadata.type
	//metadata.stringValue
	
扫描的Demo可以查看[Mockingbird](https://github.com/lcepy/Mockingbird)
	
##在Objective-C项目中使用

runpath $(inherited)@executable_path/Frameworks

三步走图片，可查

![](http://7qncz5.com1.z0.glb.clouddn.com/iOS/bridging-header-file.png)

![](http://7qncz5.com1.z0.glb.clouddn.com/iOS/defines-module-yes.png)

![](http://7qncz5.com1.z0.glb.clouddn.com/iOS/import-swift-file.png)


