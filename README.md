# GRPC
## grpc spring boot starter源码解析
### 拦截器
拦截器设置流程：
1. 创建registry bean
	@PostConstruct
	GlobalClientInterceptorRegistry init()
	从app中获取annoaconfiguer 列表，从configure中获取拦截器列表然后添加到自己的List<ClientInterceptor> clientInterceptors
	
2.  创建annoainterceptor bean，被1的@PostConstruct调用到add方法
	AnnotationGlobalClientInterceptorConfigurer addClientInterceptors（）
			this.context.getBeansWithAnnotation(GrpcGlobalClientInterceptor.class)
			调用GlobalClientInterceptorRegistry 增加拦截器列表
	
2. GrpcClientBeanPostProcessor中调用AbstractChannelFactory createChannel
    得到custominterceptors
	加上定义的注解拦截器：final List<ClientInterceptor> interceptors =
                Lists.newArrayList(this.globalClientInterceptorRegistry.getClientInterceptors());
	判断有没有spring注解AnnotationAwareOrderComparator。
	有的话interceptors.sort(AnnotationAwareOrderComparator.INSTANCE);
	返回创建好的channel ClientInterceptors.interceptForward(channel, interceptors);
	
3. 反射注入channel

-----
自定义拦截器：
1. annoa
2. ctx.getBeansWithAnnotation
3. 获取ctx中GlobalClientInterceptorRegistry， addClientInterceptors
4. 在bean GrpcClientBeanPostProcessor postProcessBeforeInitialization之前设置好

Or

1. annoa
2. 自定义AnnotationGlobalClientInterceptorConfigurer，实现add方法
3. 创建该bean,同时注意拦截器顺序
