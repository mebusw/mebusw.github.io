title: Java NIO Socket实现C/S架构
tags:
	- java
	- socket
	- nio
categories:
  - programming
  - java
---

<!--more-->

一般的Server端

```[java]
package thread.socket;  
import java.io.*;  
import java.net.*;  
import java.util.*;  
public class Server extends ServerSocket {  
    private static final int SERVER_PORT = 10000;  
    private List<WorkerThread> workers;  
      
    public Server() throws IOException {  
        super(SERVER_PORT);  
        workers = new LinkedList<WorkerThread>();  
          
        try {  
            System.out.println("server is listening...");  
            while (true) {  
                Socket socket = accept();  
                workers.add(new WorkerThread(socket));  
                System.out.println(String.format("new worker created, total %d", getWorkerCount()));  
            }  
        } catch (IOException e) {  
        } finally {  
            close();  
        }  
    }  
    public synchronized int getWorkerCount() {  
        return workers.size();  
    }  
    public static void main(String[] args) throws IOException {  
        new Server();  
    }  
}  
// --- WorkerThread  
class WorkerThread extends Thread {  
    private Socket client;  
    private BufferedReader in;  
    private PrintWriter out;  
    public WorkerThread(Socket s) throws IOException {  
        System.out.println(String.format("create a new thread. %s", s));  
        client = s;  
        in = new BufferedReader(new InputStreamReader(client  
                .getInputStream(), "GB2312"));  
        out = new PrintWriter(client.getOutputStream(), true);  
        out.println("--- Welcome ---" + client.getRemoteSocketAddress());  
        start();  
    }  
    public void run() {  
        try {  
            String line = in.readLine();  
            while (!line.equals("bye")) {  
                System.out.println("client " + client.getRemoteSocketAddress() + " says: " + line);  
                String msg = createMessage(line);  
                out.println(msg);  
                line = in.readLine();  
            }  
            out.println("bye");  
            System.out.println("client " + client.getRemoteSocketAddress() + " quit");  
            client.close();  
        } catch (IOException e) {  
        }  
    }  
    private String createMessage(String line) {  
        // ;  
        return "response to " + line;  
    }  
}  
```

 
使用NIO的Server端 （从1.5开始，Java对InputStream/OutputStream 进行了重新改写，用的就是NIO，因此，就算你不显示声明要用NIO，只要你的类继承了InputStream/OutputStream就已经在用NIO了）

``` [java]
import java.io.BufferedWriter;  
import java.io.FileInputStream;  
import java.io.IOException;  
import java.io.OutputStreamWriter;  
import java.io.PrintWriter;  
import java.net.InetSocketAddress;  
import java.net.ServerSocket;  
import java.nio.ByteBuffer;  
import java.nio.CharBuffer;  
import java.nio.channels.FileChannel;  
import java.nio.channels.SelectionKey;  
import java.nio.channels.Selector;  
import java.nio.channels.ServerSocketChannel;  
import java.nio.channels.SocketChannel;  
import java.nio.charset.Charset;  
import java.nio.charset.CharsetDecoder;  
import java.util.Iterator;  
import java.util.LinkedList;  
import java.util.Set;  
public class SelectorServer   
{  
    private static int DEFAULT_SERVERPORT = 6018;//默认端口  
    private static int DEFAULT_BUFFERSIZE = 1024;//默认缓冲区大小为1024字节  
    private static String DEFAULT_CHARSET = "GB2312";//默认码集  
    private static String DEFAULT_FILENAME = "bigfile.dat";  
    private ServerSocketChannel channel;  
    private LinkedList<SocketChannel> clients;  
    private Selector selector;//选择器  
    private ByteBuffer buffer;//字节缓冲区  
    private int port;  
    private Charset charset;//字符集  
    private CharsetDecoder decoder;//解码器  
      
      
    public SelectorServer(int port) throws IOException  
    {  
        this.port = port;  
        this.clients = new LinkedList<SocketChannel>();  
        this.channel = null;  
        this.selector = Selector.open();//打开选择器  
        this.buffer = ByteBuffer.allocate(DEFAULT_BUFFERSIZE);  
        this.charset = Charset.forName(DEFAULT_CHARSET);  
        this.decoder = this.charset.newDecoder();  
          
    }  
      
     private class HandleClient   
     {  
         private String strGreeting = "welcome to VistaQQ";  
         public HandleClient() throws IOException   
         {  
         }  
         public String readBlock()   
         {//读块数据  
             return this.strGreeting;  
         }  
         public void close()   
         {  
               
         }  
    }  
    protected void handleKey(SelectionKey key) throws IOException  
    {//处理事件  
          if (key.isAcceptable())   
          { // 接收请求  
              ServerSocketChannel server = (ServerSocketChannel) key.channel();//取出对应的服务器通道  
              SocketChannel channel = server.accept();  
              channel.configureBlocking(false);  
              channel.register(selector, SelectionKey.OP_READ);//客户socket通道注册读操作  
          }  
          else if (key.isReadable())   
          { // 读信息  
              SocketChannel channel = (SocketChannel) key.channel();  
              int count = channel.read(this.buffer);  
              if (count > 0)   
              {  
                this.buffer.flip();  
                CharBuffer charBuffer = decoder.decode(this.buffer);  
                System.out.println("Client >>" + charBuffer.toString());  
                SelectionKey wKey = channel.register(selector,  
                    SelectionKey.OP_WRITE);//为客户sockt通道注册写操作  
                wKey.attach(new HandleClient());  
              }   
              else  
              {//客户已经断开  
                channel.close();  
              }  
              this.buffer.clear();//清空缓冲区  
         }  
         else if (key.isWritable())   
         { // 写事件  
              SocketChannel channel = (SocketChannel) key.channel();  
              HandleClient handle = (HandleClient) key.attachment();//取出处理者  
              ByteBuffer block = ByteBuffer.wrap(handle.readBlock().getBytes());  
              channel.write(block);  
             // channel.socket().getInputStream().(block);  
//              PrintWriter out = new PrintWriter(new BufferedWriter(new OutputStreamWriter(  
//                        channel.socket().getOutputStream())), true);  
//              out.write(block.toString());  
        }  
    }  
    public void listen() throws IOException  
    { //服务器开始监听端口，提供服务  
        ServerSocket socket;  
        channel = ServerSocketChannel.open(); // 打开通道  
        socket = channel.socket();   //得到与通到相关的socket对象  
        socket.bind(new InetSocketAddress(port));   //将scoket榜定在制定的端口上  
        //配置通到使用非阻塞模式，在非阻塞模式下，可以编写多道程序同时避免使用复杂的多线程  
        channel.configureBlocking(false);      
        channel.register(selector, SelectionKey.OP_ACCEPT);  
        try   
        {  
            while(true)   
            {//     与通常的程序不同，这里使用channel.accpet()接受客户端连接请求，而不是在socket对象上调用accept(),这里在调用accept()方法时如果通道配置为非阻塞模式,那么accept()方法立即返回null，并不阻塞  
                this.selector.select();  
                Iterator iter = this.selector.selectedKeys().iterator();  
                while(iter.hasNext())  
                {  
                    SelectionKey key = (SelectionKey)iter.next();  
                    iter.remove();  
                    this.handleKey(key);  
                      
                }  
            }  
        }   
        catch(IOException ex)  
        {  
            ex.printStackTrace();  
        }  
    }  
    public static void main(String[] args) throws IOException   
    {  
        System.out.println("服务器启动");  
        SelectorServer server = new SelectorServer(SelectorServer.DEFAULT_SERVERPORT);  
        server.listen(); //服务器开始监听端口，提供服务  
    }  
}  
```
 
使用NIO的Client端
``` [java]
package thread.socket;  
import java.io.*;  
import java.net.*;  
public class Client {  
    Socket socket;  
    BufferedReader in;  
    PrintWriter out;  
    public Client() {  
        try {  
            socket = new Socket("127.0.0.1", 10000);  
            in = new BufferedReader(new InputStreamReader(socket  
                    .getInputStream()));  
            out = new PrintWriter(socket.getOutputStream(), true);  
            BufferedReader line = new BufferedReader(new InputStreamReader(  
                    System.in));  
            String cmd = "";  
            while (!cmd.equals("bye")) {  
                System.out.println("server says: " + in.readLine());  
                out.println(cmd = line.readLine());  
            }  
            System.out.println("socket " + socket + " stop");  
            line.close();  
            out.close();  
            in.close();  
            socket.close();  
        } catch (IOException e) {  
        }  
    }  
    public static void main(String[] args) {  
        new Client();  
    }  
}  
```