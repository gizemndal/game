import com.mysql.jdbc.Connection;
import com.sun.javafx.iio.gif.GIFImageLoader2;
import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.Rectangle;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;
import java.awt.geom.Path2D;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.sql.PreparedStatement;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.logging.Level;
import java.util.logging.Logger;
import javafx.scene.text.Text;
import javax.imageio.ImageIO;
import javax.imageio.stream.FileImageInputStream;
import javax.swing.ImageIcon;
import javax.swing.JComponent;
import javax.swing.JDialog;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JTextPane;
import javax.swing.Timer;
import javax.swing.UIManager;
import javax.swing.plaf.ColorUIResource;
import javax.swing.text.Document;
import sun.audio.AudioPlayer;
import sun.audio.AudioStream;

//Ateş classı oluşturuyoruz

class Fire{
    
    private int x; // atesin x koordinatı
    private int y; // atesin y koordinatı
    
    
    public Fire(int x, int y) {
        this.x = x;
        this.y = y; //ateşlerimiz sadece yukarı hareket edecek
    }

    public int getX() {
        return x;
    }

    public void setX(int x) {
        this.x = x;
    }

    public int getY() {
        return y;
    }

    public void setY(int y) {
        this.y = y;
    }
    
    
}


//KeyListener interface'i bir tuşa bastığımızda gerekli olan methodları kullanmamazı sağlıyor
//Toplarımızı ve uzay mekiğimizi hareket ettirmek için ActionListener interface'ini ekledik

public class Game extends JPanel implements KeyListener,ActionListener{
   
    
    
    Timer timer = new Timer(25, this); //birinci değer timer ne kadarda bir çalışacağı,ikincisi de action interface objesi
    private int time = 0; //oyunda geçen süre
    private int fire_count = 0;
  
    
    private BufferedImage image; //BufferedImage classından obje oluşturduk. Uzay gemisini eklemek için.
    private BufferedImage image2;
    private BufferedImage image3;
    private BufferedImage image4;
    private BufferedImage image1;
    private BufferedImage image5;
    private BufferedImage image6;
    private BufferedImage image7;
    private BufferedImage image8;
    private BufferedImage image9;
    private BufferedImage image10;
    private BufferedImage image11;
    private BufferedImage image12;
    
    private ArrayList<Fire> allfire = new ArrayList<Fire>();
    
    //Ateş sadece yukarı gittiği için timer çalıştığında bir ileri götürmeliyiz
    private int firedirY = 20; // timer çalıştığında ateşlerin y koordinatina ekleyeceğiz.
    private int ballXs=1000;
    private int ballX2s=1000;
    private int ballX3s=1000;
    private int barrierxs=995;
    private int barrierx1=-120; //11
    private int ballX = 0; // topun sağa sola gitmesini ayarlayacağız. topX sürekli değişecek.
    private int ballY = 3;
    private int ballX2 = 0; //2.topun X değeri
    private int ballY2 = 30; //2. topun Y değeri
    private int ballX3 = 0;
    private int ballY3 = 20;
    private int barrierX = 0;
    private int barrierY = 80;
    private int barrierX1s = 995; //12
    private int barrierY1 = 120;
    private int balldirX = 9; // sürekli topX e ekleyeceğiz. Sağda belli bir limite çarpınca sola dönecek
    private int balldirXs = -9; // sürekli topX e ekleyeceğiz. Sağda belli bir limite çarpınca sola dönecek
    private int balldirY = 6; // topun düşeydeki hareketi
    private int balldirX2 = 15;
    private int balldirX2s = -15;
    private int balldirY2 = 12;
    private int balldirX3 = 10;
    private int balldirX3s = -10;
    private int balldirY3 = 5;
    private int barrierdirX = 15;
    private int barrierdirXs = -15;
    private int barrierdirX1 = 15;
    private int barrierdirX1s =-15;
    private int scoremagenta=0 ;
    private int scoreorange=0 ;
    private int scoregreen=0 ;
//    private int counter=0;
    private int spaceshipX = 0; //uzay gemisinin nerden başlayacağını söylüyor. ALt soldan başlatıyoruz
    private int dirspaceX = 20; //klavyede sağa basında spaceshipX+20 olacak. yani sağa kayacak
    private int score;
    
    
    
    Database db = new Database();
    public int Control(){
        for (Fire fire : allfire){
            int barrierX1;
            
            if(new Rectangle(fire.getX(),fire.getY(),10,20).intersects(new Rectangle(ballX,ballY,25,25))){ //top ve ateşin çakışmalarını kontrol ediyoruz
                score +=10;
                scoremagenta = scoremagenta + 1;
                db.playerupdate(score/6);
                return 1;
            }
            else if (new Rectangle(fire.getX(),fire.getY(),10,20).intersects(new Rectangle(ballX2,ballY2,25,25))){
                score+=20;
                scoregreen = scoregreen + 1;
                
                db.playerupdate(score/6);
                return 2;
            }
            else if (new Rectangle(fire.getX(),fire.getY(),10,20).intersects(new Rectangle(ballX3,ballY3,25,25))){
                score+=30;
                scoreorange = scoreorange +1; 
                db.playerupdate(score/6);
                return 3;
            }
            else if (new Rectangle(fire.getX(),fire.getY(),10,20).intersects(new Rectangle(barrierX,barrierY,90,10))){
                score+=0;
               
                db.playerupdate(score/6);
                return 4;
            }
            else if (new Rectangle(fire.getX(),fire.getY(),10,20).intersects(new Rectangle(barrierx1,barrierY1,90,10))){
                score+=0;
                
                db.playerupdate(score/6);
                return 5;
            }
            else if(fire_count>15){ // atış sayısı 25ten büyükse oyunu kaybedecek
                score+=0;
               
                db.playerupdate(score/6);
                return 6;
         }
       
        }
        return 0;
  
    }
    public Game() {
        
        
        try {
            //image'a png dosyasını atamam gerekiyor
            image = ImageIO.read(new FileImageInputStream(new File("game_shark.png"))); 
            image2=ImageIO.read(new FileImageInputStream(new File("game_fishsimetri.png") ));
            image3=ImageIO.read(new FileImageInputStream(new File("bluefish_s.png") ));
            image4=ImageIO.read(new FileImageInputStream(new File("fancyfish.png") ));
            image1=ImageIO.read(new FileImageInputStream(new File("arkaplan.jpg") ));
            image5=ImageIO.read(new FileImageInputStream(new File("barrier.png") ));
            image6=ImageIO.read(new FileImageInputStream(new File("images-removebg.png")));
            image7=ImageIO.read(new FileImageInputStream(new File("game_fish.png")));
            image8=ImageIO.read(new FileImageInputStream(new File("bluefish.png")));
            image9=ImageIO.read(new FileImageInputStream(new File("fancyfish_s.png")));
            image10=ImageIO.read(new FileImageInputStream(new File("barrier_s.png")));
            image11=ImageIO.read(new FileImageInputStream(new File("barrier.png")));
            image12=ImageIO.read(new FileImageInputStream(new File("barrier_s.png")));
            
            
        } catch (IOException ex) {
            Logger.getLogger(Game.class.getName()).log(Level.SEVERE, null, ex);
        }
        
        //JPanelin arka rengini ayarlıyoruz
        setBackground(Color.WHITE);
        
        timer.start(); //timer'ı başlatmak istiyorum. timer başlayınca her 5 milisaniyede bir action çalışacak
        
    }
    
    public static void PlaySound(String filepath){
        
         InputStream music;
         try {
             music = new FileInputStream(new File(filepath));
             AudioStream audios = new AudioStream(music);
             AudioPlayer.player.start(audios);
         
         } catch (Exception e) {
             JOptionPane.showMessageDialog(null, "Error");
         }
         
        
    }
    
    
    //şekillerimizi JPanel üzerine çizmek için paint ve repaint oluşturuyoruz
    //her repaint çağrıldığında paint de çağırılıyor. En son çağırıp tekrar paint edilmesini isteyeceğiz.
    @Override
    public void repaint() {
        super.repaint(); 
         
    }
    
    
    @Override
    public void paint (Graphics g) {
        
       
   
        
        
        
        super.paint(g);
        g.drawImage(image1,0,0,1000,800 ,this);
        time += 5; 
        
        int highscore=db.playerdataext();
        String high=Integer.toString(highscore);
        Color textColor = Color.blue;
        g.setColor(textColor);
        Font textFont=new Font("Georgia",Font.BOLD,20);
        g.setFont(textFont);
        g.drawString("Highscore: " + high, 15, 20);
        
        int firec=15-fire_count;
        g.drawImage(image6, 915, 685, image6.getWidth()/3, image6.getHeight()/3, this);
        Font textFontfire=new Font("Georgia",Font.BOLD,20);
        String kalanatış=Integer.toString(firec);
        g.drawString(kalanatış, 940, 728);
        g.setFont(textFontfire);
        
        
        

       //nemo
        g.drawImage(image2, ballX, ballY, image2.getWidth()/6,image2.getHeight()/6 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        g.drawImage(image7, ballXs, ballY, image2.getWidth()/6,image2.getHeight()/6 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        
        
      //dory
        g.drawImage(image3, ballX2, ballY2, image2.getWidth()/6,image2.getHeight()/6 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        g.drawImage(image8, ballX2s, ballY2, image2.getWidth()/6,image2.getHeight()/6 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        
      
       //fancy
        g.drawImage(image4, ballX3, ballY3, image2.getWidth()/4,image2.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        g.drawImage(image9, ballX3s, ballY3, image2.getWidth()/4,image2.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
       
      

        g.drawImage(image5, barrierX, barrierY, image5.getWidth()/4,image5.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        g.drawImage(image10, barrierxs, barrierY, image5.getWidth()/4,image5.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        

        g.drawImage(image11, barrierx1, barrierY1, image11.getWidth()/4,image11.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        g.drawImage(image12, barrierX1s, barrierY1, image12.getWidth()/4,image12.getHeight()/4 ,this);
        g.drawImage(image, spaceshipX, 645, image.getWidth()/4,image.getHeight()/4,this); //this, JPanel üzerinde oluşacak demek
        
        for(Fire fire : allfire){ //for each döngüsü
            
            if(fire.getY() < 0){
               
                allfire.remove(fire); 
                
                
            }
            
        }

        for(Fire fire : allfire){
           
            g.drawImage(image6, fire.getX(), fire.getY(), image6.getWidth()/8,image6.getHeight()/8 ,this);
       
        }
        
        if(Control()==1){
          
            if(time == 1000){
                timer.stop();
           String message = 
                             " Your Score : "+score+ " points \n" +
                             " Amount of Spent Fire: " + fire_count+
                             "\n Spent Time: " + time/1000.0 
                   +"Nemo :" +(scoremagenta/6)
                   +"\nFancy :" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
          
            UIManager UI=new UIManager();
          
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);
            }
            }
   
        if(Control()==2){
           
            if(time == 1000){
                timer.stop();
           String message = 
                             " Your Score : "+score/6+ " points \n" +
                             " Amount of Spent Fire: " + fire_count+
                             "\n Spent Time: " + time/1000.0
                     +"Nemo :" +(scoremagenta/6)
                   +"\nFancy :" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
            UIManager UI=new UIManager();
           
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);
            }
       
        }
        if(Control()==3){
           
            if(time == 1000){
                timer.stop();
           String message = 
                             " Your Score : "+score/6+ " points \n" +
                             " Amount of Spent Fire: " + fire_count+
                             "\nSpent Time: " + time/1000.0
                     +"Nemo :" +(scoremagenta/6)
                   +"\nFancy:" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
            UIManager UI=new UIManager();
            
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);}
            
        
        }
         
        if(Control()==4){
          
            timer.stop();
            String message1 = " Your Score : "+score/6+ " points \n" +
                              " You Lost! You hit the puffer fish ! \n"+
                              " Amount of Spent Fire: " + fire_count+
                              "\nSpent Time: " + time/1000.0
                     +"\nNemo :" +(scoremagenta/6)
                   +"\nFancy :" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
            UIManager UI=new UIManager();
          
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message1,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);
           
        }
        //new ColorUIResource(250,250,210)
        if(Control()==5){
     
            timer.stop();
            String message1 = " Your Score : "+score/6+ " points \n" +
                              " You Lost ! You hit the puffer fish ! \n"+
                              " Amount of Spent Fire: " + fire_count+
                              "\nSpent Time: " + time/1000.0
                    +"\nNemo :" +(scoremagenta/6)
                   +"\nFancy :" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
            UIManager UI=new UIManager();
           
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message1,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);
        }
            
        if (Control()==6){
            
            timer.stop();
            String message1 = " Your Score : "+score/6+ " points \n" +
                              " You Lost ! \n"+
                              " You exceed the limit of 15 bubbles ! \n" +
                              "\nSpent Time: " + time/1000.0
                   +"\nNemo :" +(scoremagenta/6)
                   +"\nFancy :" +(scoregreen/6)
                   +"\nDory :" +(scoreorange/6);
            UIManager UI=new UIManager();
           
            
            ImageIcon iconic = new ImageIcon("game_babyshark.png");
            JOptionPane.showMessageDialog(null,message1,"Shark Game",JOptionPane.INFORMATION_MESSAGE,iconic);
            UIManager.put("activeCaption", new javax.swing.plaf.ColorUIResource(Color.WHITE));
            JDialog.setDefaultLookAndFeelDecorated(true);  
            System.exit(0);
        }
 
        }
    
    
    
    //Override'lar implement edildi.Klavye ve top hareket özellikleri verildi
    @Override
    public void keyTyped(KeyEvent e) {
        
    }

    @Override
    public void keyPressed(KeyEvent e) {
        
//sol tuşa bastığımızda sola,sağ tuşa bastığımızda sağa gidecek
        int c = e.getKeyCode(); //sola/sağa basınca sola/sağa basılmış değeri dönecek
        
        if(c == KeyEvent.VK_LEFT){
            if(spaceshipX <= 0){ //frame sınırları için bunu yapıyoruz
                spaceshipX = 0; //daha fazla sola gidemezsin. 
            }
            else {
                spaceshipX -= dirspaceX; //20 birim sola kayacak
            }
        }
        else if (c == KeyEvent.VK_RIGHT){
            if(spaceshipX >= 930){
                spaceshipX = 930; //daha fazla sağa gidemez
            }
            else{
                spaceshipX += dirspaceX;
            }
        }
        else if(c == KeyEvent.VK_SPACE){
            allfire.add(new Fire(spaceshipX+21,460));
            Sound sound=new Sound();
            sound.PlaySound("baloncuk.wav");
            fire_count++; //her atese bastığımda bir artacak
        }
    }

    @Override
    public void keyReleased(KeyEvent e) {
    }

    @Override
    public void actionPerformed(ActionEvent e) {
    
        for(Fire fire : allfire){
            fire.setY(fire.getY() - firedirY); //ateslerin y değerlerini değiştiriyoruz
        }
        
        //timer her çalıştığında ballX'e balldirX'i ekleyeceğiz
         //9ekle sağa
         //-9 ekle sola
        ballX += balldirX;
          
        if(ballX>=950){

           ballXs+=balldirXs;
        }
        
      
        
        if(ballXs<=(-50)){

               ballXs=1000;
               ballX=-5;
               ballX +=balldirX;
               
               
               
              
               
        }
        
        
        ballX2 += balldirX2;
        
        if(ballX2>=950){

            ballX2s+=balldirX2s;
        }
        if(ballX2s<=-50){
            
               ballX2s=1000;
               ballX2=-5;
               ballX2 +=balldirX2;
        }
        
        ballX3 += balldirX3;
        
        if(ballX3>=950){

            ballX3s+=balldirX3s; //tersine dönüyor
        }
        if(ballX3s<=-50){
          
               ballX3s=1000;
               ballX3=-5;
               ballX3 +=balldirX3;
        }
        
        barrierX += barrierdirX;
        
        if(barrierX >= 950){
          

            barrierxs+=barrierdirXs; //tersine dönüyor
        }
        if(barrierxs <= -50){
            
               barrierxs=995;
               barrierX=-5;
               barrierX +=barrierdirX;
        }
        
      
        barrierX1s += barrierdirX1s; ///995-15-...
        
        
        if(barrierX1s <= -50){
               
               barrierx1+=barrierdirX1;
               
        }
         if(barrierx1 >= 950){

            barrierX1s=950;
            barrierx1=-120;
            barrierX1s +=barrierdirX1s;
             //tersine dönüyor
        }
        
        
        
        ballY += balldirY;
        
        if(ballY >= 39){
              
            balldirY = -balldirY;
        }
        
        if(ballY <= 3){
            balldirY = -balldirY;
        }
        
         ballY2 += balldirY2;
        
        if(ballY2 >= 45){
            balldirY2 = -balldirY2;
        }
        
        if(ballY2 <= 3){
            balldirY2 = -balldirY2;
        }
        
        ballY3 += balldirY3;
        
        if(ballY3 >= 60){
            balldirY3 = -balldirY3;
        }
        
        if(ballY3 <= 3){
            balldirY3 = -balldirY3;
        }
        
        //repainti çalıştırmam gerekli.Dolayısıyla her seferinde paint çalışacak
        
        repaint();
    }

    
    
}
