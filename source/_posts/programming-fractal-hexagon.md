title: 六边形分形程序
date: 2009-02-28 21:07:54
tags:
	- java
	- fractal
categories:
    - programming
    - java	
---

![](http://s3.cn.bing.net/th?id=OJ.qNeKsKWO2BSwxQ&w=120&h=120&c=8&rs=1&pid=5.1)

玩桌游时，想到写个程序自动生成卡坦岛那样的，以正六边形为单位的地图。
可以生成任意层数的地图。


<!--more-->

``` [java]
import javax.swing.*;  
import java.awt.*;  
import java.awt.event.*;  
import java.lang.Math.*;  
public class MyPaint extends JFrame {  

    public MyPaint(String title){  
        super(title);  
    }  
      
    Container container = getContentPane();  
    DrawingPanel panel = new DrawingPanel();  
    public void go(){  
        setSize(500, 500);  
        setLocation(200, 200);  
        container.add(panel);  
        setVisible(true);  
        addWindowListener(  
                new WindowAdapter(){  
                    public void windowClosing(WindowEvent e){  
                        System.exit(0);  
                    }  
                });  
    }  
    public static void main(String[] args) {  
          
        try{  
            UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());  
        }  
        catch (Exception e){  
            System.out.print(e);  
        }  
          
        MyPaint paint = new MyPaint("java painter");  
        paint.go();  
          
    }  
}  
class DrawingPanel extends JPanel{  
    static double R = 30;  
    int oldX, oldY, newX, newY;  
    Color color = Color.blue;  
    public DrawingPanel(){  
        setBackground(Color.white);  
        addMouseListener(new MyMouseListener());  
        addMouseMotionListener(new MyMouseListener());  
          
    }  
      
    public void paint(Graphics g){  
        //g.drawArc(10, 10, 10, 10, 10, 190);  
        g.setColor(color);  
        g.drawLine(oldX, oldY, newX, newY);  
          
        //specify the max level of map  
        drawMap(g, 6);  
          
    }  
      
    /* 
     * draw a map of Catan  
     * @param g 
     */  
    private void drawMap(Graphics g, int maxLevel) {  
          
        double r = R * Math.pow(3, 0.5) / 2;  
        double offsetX = R * (maxLevel * 2+ 1) + 5;  
        double offsetY = r * (maxLevel * 2+ 1) + 5;  
        drawPoly6(g, 0 + offsetX, 0 + offsetY);     //the central one  
        double xi[] = new double[6];  
        double yi[] = new double[6];  
        double tempx, tempy;  
        for(int level = 1; level <= maxLevel; level++)   {  
              
            for(int j = 0; j < 6; j++)   {  
                xi[j] = (2 * level * r) * Math.cos(Math.PI / 6 + Math.PI / 3 * j);  
                yi[j] = (2 * level * r) * Math.sin(Math.PI / 6 + Math.PI / 3 * j);  
                //drawPoly6(g, xi[j] + offsetX, yi[j] + offsetY);  
                g.drawString(String.valueOf(level), (int)(xi[j] + offsetX), (int)(yi[j] + offsetY));  
            }  
              
            for(int k = 1; k <= level; k++)  {  
                for(int j = 0; j < 6; j++)   {                 
                //Interpolation  
                tempx = xi[(j + 1) % 6];  
                tempy = yi[(j + 1) % 6];  
                drawPoly6(g,   
                          k * (tempx - xi[j]) / level + xi[j] + offsetX,   
                          k * (tempy - yi[j]) / level + yi[j] + offsetY);  
                }             
            }  
        }  
          
    }  
      
    private void drawPoly6(Graphics g, double x, double y) {  
        int xx[] = new int[6];  
        int yy[] = new int[6];  
        for(int i = 0; i < 6; i++)   {  
            xx[i] = (int) (x + R * Math.cos(Math.PI / 3 * i));  
            yy[i] = (int) (y + R * Math.sin(Math.PI / 3 * i)); Math.round(1);  
              
            System.out.println(xx[i] + ":" + yy[i]);  
        }  
          
        g.drawPolygon(xx, yy, xx.length);  
          
    }  
      
    class MyMouseListener extends MouseAdapter implements MouseMotionListener{  
        public void mousePressed(MouseEvent e){  
            oldX = newX = e.getX();  
            oldY = newY = e.getY();  
        }  
        public void mouseDragged(MouseEvent e){  
            oldX = newX;  
            oldY = newY;  
            newX = e.getX();  
            newY = e.getY();  
            repaint();  
              
        }  
    }  
      
} 
```