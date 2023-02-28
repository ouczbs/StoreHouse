# 平台层

# 核心层

## 数学库

## 数据结构

## 反射

### 动态反射

运行时反射：在运行时创建反射的注册表，将所有要反射的类写入注册表中。

反射数据存在实例数据中

### 静态反射

编译期反射：给所有要反射的类指定宏，并在编译期自动生成反射类的代码。

反射数据存在代码数据中

## 序列化

## 日志

```C++
/*
#https://zhuanlan.zhihu.com/p/586952566
#https://blog.csdn.net/shizheng163/article/details/79418190
设置输出模式 颜色%$
console_sink->set_pattern("[%H:%M:%S.%e] [%l] %v%$");
日志正文格式 fmt格式 "format info with cood {1},{0} ", 0, 1
    format_spec 格式 {:#x} {:#X} //...
    #https://zhuanlan.zhihu.com/p/424862897
*/
using namespace Piccolo;
auto infotest = LogSystem::LogLevel::info;
#define LOG_HELPER(LOG_LEVEL, ...) \
    m_logger_system->log(LOG_LEVEL, "[" + std::string(__FUNCTION__) + "] " + __VA_ARGS__);
#define LOG_DEBUG(...) LOG_HELPER(LogSystem::LogLevel::debug, __VA_ARGS__);
#define LOG_INFO(...) LOG_HELPER(LogSystem::LogLevel::info, __VA_ARGS__);
typedef unsigned int ADDR_TYPE;
#define ADDR_SMARTPOINT(SMARTPOINT) (ADDR_TYPE)SMARTPOINT.get()
#define ADDR(POINT) (ADDR_TYPE)POINT
```





# 资源层

## 资源到资产



# 功能层

## 窗口

- 创建窗口
- 监听窗口

## 渲染

结构

- WindowSystem
- RenderSystem
  - VulkanRHI
  - GlobalRenderingRes
  - RenderResource

### RHI



### Instance

API实例，辅助创建其他对象

```c++
//创建 & 初始化
VkApplicationInfo{};
VkInstanceCreateInfo { pApplicationInfo = &appInfo };
vkCreateInstance(&instance_create_info, nullptr, &m_instance);
```

### Surface

```c++
//创建 & 初始化
glfwCreateWindowSurface(m_instance, m_window, nullptr, &m_surface);

vkGetPhysicalDeviceSurfaceCapabilitiesKHR(physicalm_device, m_surface, &details_result.capabilities);
vkGetPhysicalDeviceSurfaceFormatsKHR(physicalm_device, m_surface, &format_count, nullptr);
vkGetPhysicalDeviceSurfacePresentModesKHR(physicalm_device, m_surface, &presentmode_count, nullptr);
```

### PhysicalDevice

```c++
//创建 & 初始化
vkEnumeratePhysicalDevices(m_instance, &physical_device_count, physical_devices.data());
m_physical_device =>> GetBestDevices(physical_devices);
```

### LogicDevice

设备实例，辅助创建其他对象

```c++
//创建 & 初始化
VkDeviceQueueCreateInfo{};
VkPhysicalDeviceFeatures{};
VkDeviceCreateInfo{pEnabledFeatures = &VkPhysicalDeviceFeatures , pQueueCreateInfos = &VkDeviceQueueCreateInfo}
vkCreateDevice(m_physical_device, &device_create_info, nullptr, &m_device);

//创建队列 & 初始化
VkQueue vk_graphics_queue;
vkGetDeviceQueue(m_device, m_queue_indices.graphics_family.value(), 0, &vk_graphics_queue);
m_graphics_queue = new VulkanQueue();
((VulkanQueue*)m_graphics_queue)->setResource(vk_graphics_queue);

vkGetDeviceQueue(m_device, m_queue_indices.present_family.value(), 0, &m_present_queue);

VkQueue vk_compute_queue;
vkGetDeviceQueue(m_device, m_queue_indices.m_compute_family.value(), 0, &vk_compute_queue);
m_compute_queue = new VulkanQueue();
((VulkanQueue*)m_compute_queue)->setResource(vk_compute_queue);

// more efficient pointer
_vkResetCommandPool      = (PFN_vkResetCommandPool)vkGetDeviceProcAddr(m_device, "vkResetCommandPool");
_vkBeginCommandBuffer    = (PFN_vkBeginCommandBuffer)vkGetDeviceProcAddr(m_device, "vkBeginCommandBuffer");
_vkEndCommandBuffer      = (PFN_vkEndCommandBuffer)vkGetDeviceProcAddr(m_device, "vkEndCommandBuffer");
//...
```

### CommandPool

可以创建多个，一个公用，一个数组循环使用

```c++
//创建命令池 & 初始化
VkCommandPool vk_command_pool;
VkCommandPoolCreateInfo command_pool_create_info {};
command_pool_create_info.sType            = VK_STRUCTURE_TYPE_COMMAND_POOL_CREATE_INFO;
command_pool_create_info.pNext            = NULL;
command_pool_create_info.flags            = VK_COMMAND_POOL_CREATE_RESET_COMMAND_BUFFER_BIT;
command_pool_create_info.queueFamilyIndex = m_queue_indices.graphics_family.value();
vkCreateCommandPool(m_device, &command_pool_create_info, nullptr, &vk_command_pool)
```

### CommandBuffer

```c++
//创建命令缓冲 & 初始化
VkCommandBufferAllocateInfo command_buffer_allocate_info {};
command_buffer_allocate_info.sType              = VK_STRUCTURE_TYPE_COMMAND_BUFFER_ALLOCATE_INFO;
command_buffer_allocate_info.level              = VK_COMMAND_BUFFER_LEVEL_PRIMARY;
command_buffer_allocate_info.commandBufferCount = 1U;

for (uint32_t i = 0; i < k_max_frames_in_flight; ++i)
{
    command_buffer_allocate_info.commandPool = m_command_pools[i];
    vkAllocateCommandBuffers(m_device, &command_buffer_allocate_info, &m_vk_command_buffers[i])
}
```

### CommandSync

```c++
VkSemaphoreCreateInfo semaphore_create_info {};
semaphore_create_info.sType = VK_STRUCTURE_TYPE_SEMAPHORE_CREATE_INFO;

VkFenceCreateInfo fence_create_info {};
fence_create_info.sType = VK_STRUCTURE_TYPE_FENCE_CREATE_INFO;
fence_create_info.flags = VK_FENCE_CREATE_SIGNALED_BIT; // the fence is initialized as signaled

for (uint32_t i = 0; i < k_max_frames_in_flight; i++)
{
    m_image_available_for_texturescopy_semaphores[i] = new VulkanSemaphore();
    vkCreateSemaphore(m_device, &semaphore_create_info, nullptr, &m_image_available_for_render_semaphores[i]) 
    vkCreateSemaphore(m_device, &semaphore_create_info, nullptr, &m_image_finished_for_presentation_semaphores[i]) 
    vkCreateSemaphore(m_device, &semaphore_create_info, nullptr, &(((VulkanSemaphore*)m_image_available_for_texturescopy_semaphores[i])->getResource())) 
    vkCreateFence(m_device, &fence_create_info, nullptr, &m_is_frame_in_flight_fences[i])
}
```


### DescriptorPool

```c++
VkDescriptorPoolSize pool_sizes[7];
pool_sizes[0].type            = VK_DESCRIPTOR_TYPE_STORAGE_BUFFER_DYNAMIC;
pool_sizes[0].descriptorCount = 3 + 2 + 2 + 2 + 1 + 1 + 3 + 3;

VkDescriptorPoolCreateInfo pool_info {};
pool_info.sType         = VK_STRUCTURE_TYPE_DESCRIPTOR_POOL_CREATE_INFO;
pool_info.poolSizeCount = sizeof(pool_sizes) / sizeof(pool_sizes[0]);
pool_info.pPoolSizes    = pool_sizes;

vkCreateDescriptorPool(m_device, &pool_info, nullptr, &m_vk_descriptor_pool)
```

### SwapChain

ImageView

FrameBuffer

```c++
VkSwapchainCreateInfoKHR createInfo {};
createInfo.sType   = VK_STRUCTURE_TYPE_SWAPCHAIN_CREATE_INFO_KHR;
createInfo.surface = m_surface;
createInfo.minImageCount    = image_count;

vkCreateSwapchainKHR(m_device, &createInfo, nullptr, &m_swapchain);
vkGetSwapchainImagesKHR(m_device, m_swapchain, &image_count, nullptr);
m_swapchain_images.resize(image_count);
vkGetSwapchainImagesKHR(m_device, m_swapchain, &image_count, m_swapchain_images.data());

m_swapchain_imageviews.resize(m_swapchain_images.size());
// create imageview (one for each this time) for all swapchain images
for (size_t i = 0; i < m_swapchain_images.size(); i++)
{
    VkImageView vk_image_view;
    vk_image_view = VulkanUtil::createImageView(m_device,
	m_swapchain_images[i],(VkFormat)m_swapchain_image_format,
 	VK_IMAGE_ASPECT_COLOR_BIT, VK_IMAGE_VIEW_TYPE_2D,1,1);
}
VulkanUtil::createImage(m_physical_device,
                        m_device,
                        m_swapchain_extent.width,
                        m_swapchain_extent.height,
                        (VkFormat)m_depth_image_format,
                        VK_IMAGE_TILING_OPTIMAL,
                        VK_IMAGE_USAGE_INPUT_ATTACHMENT_BIT | VK_IMAGE_USAGE_DEPTH_STENCIL_ATTACHMENT_BIT |
                        VK_IMAGE_USAGE_TRANSFER_SRC_BIT,
                        VK_MEMORY_PROPERTY_DEVICE_LOCAL_BIT,
                        ((VulkanImage*)m_depth_image)->getResource(),
                        m_depth_image_memory,
                        0,
                        1,
                        1);
```

### VmaAllocator

```c++
VmaVulkanFunctions vulkanFunctions    = {};
vulkanFunctions.vkGetInstanceProcAddr = &vkGetInstanceProcAddr;
vulkanFunctions.vkGetDeviceProcAddr   = &vkGetDeviceProcAddr;

VmaAllocatorCreateInfo allocatorCreateInfo = {};
allocatorCreateInfo.vulkanApiVersion       = m_vulkan_api_version;
allocatorCreateInfo.physicalDevice         = m_physical_device;
allocatorCreateInfo.device                 = m_device;
allocatorCreateInfo.instance               = m_instance;
allocatorCreateInfo.pVulkanFunctions       = &vulkanFunctions;

vmaCreateAllocator(&allocatorCreateInfo, &m_assets_allocator);
```



























# 工具层



# 插件层



