```java
import java.awt.*;

class LabelText  {
  public static void main(String[] args){
        Frame f = new Frame("TextField Example");

	Label l1 = new Label("Username");
        l1.setBounds(90, 50, 100, 30);	
        TextField t1 = new TextField();
        t1.setBounds(290, 50, 100, 30);
	Label l2 = new Label("Password");
        l2.setBounds(90, 100, 100, 30);	
        TextField t2 = new TextField();
        t2.setBounds(290, 100, 100, 30);
	
        f.add(t1);
        f.add(t2);
	f.add(l1);
	f.add(l2);
        f.setSize(500, 500);
        f.setLayout(null);
        f.setVisible(true);
    }
}
```
