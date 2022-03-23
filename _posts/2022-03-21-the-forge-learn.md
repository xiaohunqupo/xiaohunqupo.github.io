---
titile: 学习 The-Forge 笔记
tags: The-Forge
---

# 独立程序

床啊及独立程序需要执行以下步骤：

1. 从`IApp`接口类派生自定义应用类。`IApp`类定义了程序主入口、创建窗体、初始化绘制信息、更新信息、退出等接口。

2. 使用`DEFINE_APPLICATION_MAIN`宏注册自定义应用类。
   
   ```cpp
   #define DEFINE_APPLICATION_MAIN(appClass)                         \
       int IApp::argc;                                               \
       const char** IApp::argv;                                      \
       extern int WindowsMain(int argc, char** argv, IApp* app);     \
                                                                     \
       int main(int argc, char** argv)                               \
       {                                                             \
           IApp::argc = argc;                                        \
           IApp::argv = (const char**)argv;                          \
           appClass app;                                             \
           return WindowsMain(argc, argv, &app);                     \
       }
   ```
   
   其中`WindowsMain`执行流程：

```plantuml
@startuml
start
:initMemAlloc();
note left:初始化内存分配器
:FileSystemInitDesc;
note left:定义文件系统对象
:initFileSystem();
note left:初始化文件系统
:fsSetPathForResourceDir();
note left:设置资源搜索目录
:initLog();
note left:初始化日志
:initWindowClass();
note left:初始化窗口类
:更新APP的Setting;
:getMonitor();
note left:获取监视器设置
:创建并设置窗口定义对象WindowDesc;
:openWindow();
note left:创建窗口
:initBaseSubsystems();
note left:初始化子系统
:initTimer();
note left:初始化计时器
:pApp-><color:red>Init();
note left:应用程序初始化
:setupAPISwitchingUI
note left:安装API切换UI
:pApp-><color:red>Load();
note left:加载App
while(quit) is (not)
 if (gResetScenario == RESET_SCENARIO_NONE) then (yes)
  :handleMessages();
  note left:获取消息
  :updateBaseSubsystems();
  noteleft:更新子系统
  :pApp-><color:red>Update();
  :pApp-><color:red>Draw();
 else (no)
  :pApp-><color:red>Unload();
  :pApp-><color:red>Exit();
  :exitBaseSubsystems;
  stop
 endif
endwhile (true)
:pApp->mSetting.mQuit = true;
:pApp->Unload();
note left:释放管道、交换链、渲染对象
:pApp->Exit();
note left:卸载资源、卸载文件系统、输入系统、交互系统
:exitWindowClass;
note left:退出窗口类
:exitLog();
note left:退出日志系统
:exitBaseSubsystem();
note left:退出子系统
:exitFileSystem();
note left:退出文件系统
:exitMemAlloc();
note left:退出内存分配器

end
@enduml
```

# IApp的接口

## Init

该接口用于初始化程序，一般流程如下

```plantuml
@startuml
start
:fsSetPathForResourcedir();
note left:设置资源目录
:构造RenderDesc;
:initRender();
note left:初始化渲染器
:构造QueueDesc;
while (ImageCount)
:addCmdPool;
 note right:添加命令池
 :addCmd;
 note right:添加命令
 :addFence;
 note right:添加栅格器
 :addSemaphore;
 note right:添加(完成)信号
endWhile
:addSemaphore;
note right:添加(完成)信号
:initScreenshotInterface;
note left:初始化屏幕快照接口
:initResourceLoaderInterface;
note right:初始化资源读取接口
:addResource(TextrueLoadDesc);
note right:添加资源
:addShader(ShaderLoadDesc);
note right:添加着色器
:addSampler(SamplerDesc);
note right:添加采样器
:addRootSignature(RootSignatureDesc);
note right:添加根签名
:addDescriptorSet(DescriptorSetDesc);
note right:添加解释器集合
:addResource(BufferLoadDesc);
note right:添加资源
:fntDefineFonts(FontDesc);
note right:定义字体
:initFontSystem(FontSystemDesc);
note right:初始化字体系统
:initUserInterface(UserInterfaceDesc);
note right:初始化Forge用户交互
:initProfiler(ProfilerDesc);
note right:初始化分析器和UI
:addGpuProfiler;
note right:添加GPU分析器
:uiCreateComponent(UIComponentDesc);
note right:创建UI组件
:luaRegisterWidget(UIWidget);
note right:注册ui组件，依赖lua
:waitForAllResourceLoads();
note right:等待所有资源读取
:cameraMotionParameters;
note right:镜头运动参数
:initFpsCameraController();
note right:初始化镜头控制器
:pCameraController->setMotionParameters();
note right:设置运动参数
:initInputSystem(InputSystemDesc);
note right:初始化输入系统
:addInputAction(InputActionDesc);
note right:注册输入动作
:updateDescriptorSet();
note right:更新解释器集合

end
@enduml
```

## Load

该接口用于添加管道、交换链、深度缓冲区等

```plantuml
@startuml
start
:addSwapChain(SwapChainDesc);
note right:添加交换链
:addRenderTarget(RenderTargetDesc);
note right:添加深度缓冲区(貌似不加也可以)
:addFontSystemPipelines(RenderTarget);
note right:添加字体系统管道
:addUserInterfacePipelines(PipelineDesc);
note right:添加用户交互管道
:addPipeline(PipelineDesc);
note right:添加绘制管道
end
@enduml
```

用户交互管道的组成及基本设置如下:

```plantuml
@startuml
class PipelineDesc{
+ pCache : PipelineCache*
+ mType : PipelineType = PIPELINE_TYPE_GRAPHICS
+ mGraphicsDesc : GraphicsPipelineDesc
}

class RenderTarget{
+ mWidth : uint32_t
+ mHeight : uint32_t
+ mSampleCount : SampleCount
+ mSampleQuality : unit32_t
+ mFormat : TinyImageFormat
}

class BlendStateDesc{
+ mSrcFactors : BlendConstant
+ mDstFactors : BlendConstant
+ mSrcAlphaFactors : BlendConstant
+ mDstAlphaFactors : BlendConstant
+ mMasks : int32_t
+ mRenderTargetMask : BlendStateTargets
+ mIndependentBlend : bool
}

class DepthStateDesc{
+ mDepthTest : bool
+ mDepthWrite : bool
}

class GraphicsPipelineDesc{
+ mSampleCount : Samplecount
+ mSampleQuality : uint32_t
+ pColorFormats : TinyImageFormat*
+ pRasterizerState : RasterizerStateDesc*
+ pDepthState : DepthStateDesc*
+ pBlendState : BlendStateDesc*
+ pRootSignature : RootSignature*
+ pShaderProgram : Shader*
+ pVertexLayout : VertexLayout*
}

entity UserInterface{
+ pPipelineCache : PipelineCache
+ pRootSignatureTextured : RootSignature*
+ pShaderTextured : Shader*
+ mVertexLayoutTextured : VertexLayout*
+ mWidth : float
+ mHeight : float
}

PipelineDesc::pCache o-- UserInterface::pPipelineCache
PipelineDesc::mGraphicsDesc o-- GraphicsPipelineDesc
GraphicsPipelineDesc::mSampleCount o-- RenderTarget::mSampleCount
GraphicsPipelineDesc::mSampleQuality o-- RenderTarget::mSampleQuality
GraphicsPipelineDesc::pColorFormats o-- RenderTarget::mFormat
GraphicsPipelineDesc::pBlendState o-- BlendStateDesc
GraphicsPipelineDesc::pDepthState o-- DepthStateDesc
GraphicsPipelineDesc::pRasterizerState o-- RasterizerStateDesc
GraphicsPipelineDesc::pRootSignature o-- UserInterface::pRootSignatureTextured
GraphicsPipelineDesc::pShaderProgram o-- UserInterface::pShaderTextured
GraphicsPipelineDesc::pVertexLayout o-- UserInterface::mVertexLayoutTextured
RenderTarget::mWidth o-- UserInterface::mWidth
RenderTarget::mHeight o-- UserInterface::mHeight




@enduml
```

绘制管道的组成及基本设置如下

```plantuml
@startuml
class PipelineDesc{
+ mType : PipelineType = PIPELINE_TYPE_GRAPHICS
+ mGraphicsDesc : GraphicsPipelineDesc
}

class VertexLayout{
   
}

class RasterizerStateDesc{
}

class DepthStateDesc{
    
}

class GraphicsPipelineDesc{
+ pRootSignature : RootSignature*
+ pVertexLayout : VertexLayout*
+ pDepthState : DepthStateDesc*
+ pRasterizerState : RasterizerStateDesc*
+ pColorFormats : TinyImageFormat*
+ mSampleCount : SampleCount
+ mSampleQuality : uint32_t
+ mDepthStencilFormat : TinyImageFormat
+ pShaderProgram : Shader*
}

class SwapChain{
+ ppRenderTargets : RenderTarget**
}

class Shader{

}

class RootSignature{
}

class RenderTarget{
+ mFormat : TinyImageFormat
+ mSampleCount : Samplecount
+ mSampleQuality : unint32_t    
}

SwapChain::ppRenderTargets o-- RenderTarget

PipelineDesc::mGraphicsDesc o-- GraphicsPipelineDesc 
GraphicsPipelineDesc::pDepthState o-- DepthStateDesc
GraphicsPipelineDesc::pColorFormats o-- RenderTarget::mFormat
GraphicsPipelineDesc::mSampleCount o-- RenderTarget::mSampleCount
GraphicsPipelineDesc::mSampleQuality o-- RenderTarget::mSampleQuality
GraphicsPipelineDesc::pVertexLayout o-- VertexLayout
GraphicsPipelineDesc::pRasterizerState o-- RasterizerStateDesc
GraphicsPipelineDesc::mDepthStencilFormat o-- RenderTarget::mFormat
GraphicsPipelineDesc::pRootSignature o-- RootSignature
GraphicsPipelineDesc::pShaderProgram o-- Shader

@enduml
```

## Update

主要用于更新输入系统、场景数据

```plantuml
@startuml
start
:updateInputSystem(width, height);
note right:更新输入系统
:pCameraController->update();
note right:更新镜头控制器
:更新场景|
end
@enduml
```

## Draw

Draw接口用于实现绘制每帧的内容

```plantuml
@startuml
start
:acquireNextImage(uint32_t);
note right:获取下一帧信息
:getFenceStatus(FenceStatus);
note right:获取Fence
if (FenceStatus == FENCE_STATUS_INCOMPLETE) is (yes) then
 :watiForFences;
endif
note right:等待GPU完成
:beginUpdateResource;
note right:开始更新资源
:设置数据映射;
note right:用户自定义
:endUpdateResource;
note right:结束更新资源
:resetCmdPool();
note right:重置命令池
:beginCmd();
note left:从这里开始命令
note right:开始命令
:cmdBeginGpuFrameProfile();
note right:命令-开始Gpu帧的装配
:cmdResourceBarrier(RenderTargetBarrier)
note right:命令-设置资源屏障
:cmdBindRenderTargets(LoadActionDesc)
note right:命令-绑定渲染对象-读取动作说明
:cmdSetViewport;
note right:命令-设置视口
:cmdSetScissor;
note right:命令-设置Scissor视角？

:cmdBeginGpuTimestampQuery;
note left:从这里开始一个对象的绘制流程
note right:命令-开始Gpu时间戳查询
:cmdSetViewport;
note right:命令-设置视口
:cmdBindPipeline;
note right:命令-绑定管道
:cmdBindDescriptorSet;
note right:命令-绑定声明集合
:cmdBindVertexBuffer;
note right:命令-绑定顶点集合
:cmdDraw;
note right:命令-绘制
:cmdSetViewport
note right:命令-设置视口
:cmdEndGpuTimestampQuery;
note left:绘制对象的流程结束
note right:命令-结束Gpu时间戳查询

:endcmd;
note left:到这里结束命令
note right:结束命令

:queueSubmit(QueueSubmitDesc);
note right:队列提交
:queuePresent(QueuePresentDesc);
note right:队列发布

@enduml
```

## Unload

释放管道、交换链、渲染对象

```plantuml
@startuml
start
:waitQueueIdle();
note right:等待队列闲置
:removeUserInterfacePipelines();
note right:移除用户交互管道
:removeFontSystemPipelines();
note right:移除字体系统管道
:removePipeline();
note right:移除指定管道
:removeSwapChain();
note right:移除交换链
:removeRenderTarget();
note right:移除渲染对象

end
@enduml
```

## Exit

卸载资源、卸载文件系统、输入系统、交互系统

```plantuml
@startuml
start
:exitInputSytem();
note right:退出输入系统
:exitCameraController();
note right:退出摄像机控制器
:exitUserInterface();
note right:退出用户交互
:exitFontSystem();
note right:退出字体系统
:removeResource();
note right:移除资源
:removeDescriptorSet();
note right:移除Descriptor
:removeSampler()
note right:移除采样器
:removeShader()
note right:移除着色器
:removeRootSignature()
note right:移除根信签名
:removeFence()
note right:移除Fence
:removeSemaphore();
note right:移除信标
:removeCmd();
note right:移除命令
:exitResourceLoadrInterface();
note right:移除资源读取接口
:exitRender();
note right:移除渲染器

end
@enduml
```

# 基本定义

1. AABB (Axis-Aligned Bounding Box)，轴对称齐包围盒。
   
   ![](https://img-blog.csdnimg.cn/20210816114730286.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hc3Rlcl9DdWk=,size_16,color_FFFFFF,t_70)

2. Cull，剔除

3. Blend，混合

4. Conservative，保守？

5. Stencil，模具

6. Fence，是什么呢？

7. cmdPool，命令池。通常cpu向其添加命令，Gpu从其获取命令，这是Cpu和Gpu同步的一种手段

8. Resource Barrir 资源屏障。通知图形驱动程序驱动程序可能需要同步对存储资源的内存的多次访问的情况。

9. Scissor

# 基本类

## FenceStatus




