## Netty编码流程

?> 写一个简单的http server

```java
public class TestServer {
    public static void main(String[] args) {
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup wordGroup = new NioEventLoopGroup();

        ServerBootstrap serverBootstrap = new ServerBootstrap();

        try {
            serverBootstrap.group(bossGroup,wordGroup)
                    .channel(NioServerSocketChannel.class)
                    
                    // channel初始化器
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel socketChannel) throws Exception {
                            ChannelPipeline pipeline = socketChannel.pipeline();
                            pipeline.addLast("httpServerCodec",new HttpServerCodec());
                            
                            // 在pipeline中加入自定义的解码器 handler
                            pipeline.addLast("httpServerContent", new SimpleChannelInboundHandler<HttpObject>() {
                                @Override
                                protected void channelRead0(ChannelHandlerContext channelHandlerContext, HttpObject msg) throws Exception {
                                    /* 
                                        另外有一个 LastHttpContent 类型的 EMPTY_LAST_CONTENT 对象
                                        来源：HttpServerCodec 中 inboundHandler 为 HttpServerRequestDecoder ==>  HttpObjectDecoder
                                        channelRead 的时候会进行解码，追加两个对象；
                                            out.add(message);
                                            out.add(LastHttpContent.EMPTY_LAST_CONTENT);
                                        所以追加if判断
                                    */
                                    if(msg instanceof HttpRequest){
                                        ByteBuf byteBuf = Unpooled.copiedBuffer("hello world", CharsetUtil.UTF_8);
                                        FullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1,HttpResponseStatus.OK,byteBuf);
                                        response.headers().set(HttpHeaderNames.CONTENT_TYPE,"text/plain");
                                        response.headers().set(HttpHeaderNames.CONTENT_LENGTH,byteBuf.readableBytes());
                                        response.headers().set(HttpHeaderNames.SERVER,"X-eli");

                                        channelHandlerContext.writeAndFlush(response);
                                    }
                                }
                            });
                        }
                    });
            ChannelFuture channelFuture = serverBootstrap.bind(13308).sync();
            channelFuture.channel().closeFuture().sync();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            bossGroup.shutdownGracefully();
            wordGroup.shutdownGracefully();
        }
    }
}
```





## Reference
* https://www.bilibili.com/video/BV1c4411J7Ty?p=5