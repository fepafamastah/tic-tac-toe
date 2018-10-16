# tic-tac-toe
juego del gato

package juegodelgato;



import java.util.ArrayList;
import java.util.List;
import javafx.animation.KeyFrame;
import javafx.animation.KeyValue;
import javafx.animation.Timeline;
import javafx.application.Application;
import static javafx.application.Application.launch;
import javafx.geometry.Pos;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.input.MouseButton;
import javafx.scene.layout.Pane;
import javafx.scene.layout.StackPane;
import javafx.scene.paint.Color;
import javafx.scene.shape.Line;
import javafx.stage.Stage;
import javafx.scene.shape.Rectangle;
import static javafx.scene.text.Font.font;
import javafx.scene.text.Text;
import javafx.util.Duration;
public class tictactoe extends Application{
    
    private boolean turnoX=true; // bandera para determinar el turno de quien es
    private boolean jugable=true; //determina si la partida es jugable o no 
    private List<Combinaciones> combis = new ArrayList<>(); //esta estructura permite hacer una lista de todas las posibles combinaciones y almacenarlas para su comprobación
    private espacioDeJuego [][] tablero = new espacioDeJuego[3][3];// esta estructura nos ayudará a   //TILE = ESPACIO DE JUEGO BORRAR// 
    private  Pane root = new Pane ();
    private Parent createContent(){
       
        root.setPrefSize(600,600); //tamaño de la ventana pane
        
        for(int i=0;i<3;i++){
            for(int j=0;j<3;j++){
               espacioDeJuego Tablero = new espacioDeJuego(); 
               Tablero.setTranslateX(j*200);
               Tablero.setTranslateY(i*200); 
               
               root.getChildren().add(Tablero);
               
               tablero[j][i]=Tablero;
            }
        }
         for(int y=0;y<3;y++){
           combis.add(new Combinaciones(tablero[0][y],tablero[1][y],tablero[2][y])); //con este for agregamos todas las combinaciones horizontales a la lista <>
             
             
    }
         for(int x=0;x<3;x++){
          combis.add(new Combinaciones(tablero[x][0],tablero[x][1],tablero[x][2])); //el mismo caso de arriba, solo que con las combinaciones verticales
    }
         //estas dos lineas almacenan en la lista las combinaciones de diagonales
         combis.add(new Combinaciones(tablero[0][0],tablero[1][1],tablero[2][2]));
         combis.add(new Combinaciones(tablero[2][0],tablero[1][1],tablero[0][2]));
          return root; //método para diseñar paso a paso el tablero del juego
    }

    @Override
    public void start(Stage primaryStage)throws Exception{
        primaryStage.setScene(new Scene(createContent()));
        primaryStage.show(); 
    }
    
    private void revisarEstado(){ //en este método se revisan todas las posibles combinaciones de líneas
        
        for(Combinaciones combinaciones: combis){ //pone a prueba los valores de cada casilla, validando si el juego aún es jugable o no
            if(combinaciones.juegoCompletado()){
                jugable=false;
                animacionVictoria(combinaciones);
                break; //detiene el programa, pues no es jugable
            }
        }
        
    }
    
    private void  animacionVictoria(Combinaciones combinaciones){ //toma como parametros a la lista de combinaciones ganadora
        
        Line linea = new Line(); //importamos la animación de una linea siendo dibujada 
                linea.setStartX(combinaciones.espacios[0].getCenterX());
                linea.setStartY(combinaciones.espacios[0].getCenterY());
                linea.setEndX(combinaciones.espacios[0].getCenterX());
                linea.setEndY(combinaciones.espacios[0].getCenterY());
                
                root.getChildren().add(linea);
                Timeline timeline = new Timeline(); //este metodo ayuda a traducir la funcion setEnd
                timeline.getKeyFrames().add(new KeyFrame(Duration.seconds(1),
                new KeyValue(linea.endXProperty(), combinaciones.espacios[2].getCenterX()),
                new KeyValue(linea.endYProperty(), combinaciones.espacios[2].getCenterY())));
timeline.play();
//se utiliza la funcion setEnd para no terminar con un solo punto en lugar de una linea recta a la hora de dibujar la linea
    }           
    
    //tiles=espacios BORRAR
    
    private class Combinaciones{
    
    private espacioDeJuego [] espacios;
    public Combinaciones(espacioDeJuego... espacios){ //los ... son para pasar como un arreglo elementos sin tener que crear el arreglo
        this.espacios=espacios; //this se utiliza para referirse a un mismo objeto dentro del proceso
    }
    
    public boolean juegoCompletado(){ //en este proceso se verifica si el tablero se llenó con las figutas X o O 
                                      // revisando uno por uno los espacios del tablero
        if(espacios[0].getValor().isEmpty())
               return false;
        
        return espacios[0].getValor().equals(espacios[1].getValor())
        && espacios[0].getValor().equals(espacios[2].getValor());
                
    }
    
    
    
}
    private class espacioDeJuego extends StackPane{
        
        private Text text = new Text();
        
        public espacioDeJuego(){  
             Rectangle tablero = new Rectangle(200,200);
             tablero.setFill(null);
             tablero.setStroke(Color.BLUE); //con este paso se le dio el tamaño y el color al tablero de juego
                                             //utilizando rectangulos para simular la cuadricula
             text.setFont(font(72));
             
             setAlignment(Pos.CENTER);
             getChildren().addAll(tablero,text);
             
             setOnMouseClicked(event ->{ //registra los botones tocados por los jugadores
                 if(!jugable)
                     return;
                 if(event.getButton()== MouseButton.PRIMARY){ // click izquierdo es para el jugador X
                     if(!turnoX)
                         return;
                 dibujaX(); 
                 turnoX=false;
                 revisarEstado();
             }
                 else if (event.getButton()==MouseButton.SECONDARY){ // click derecho para jugador O 
                     if(turnoX)
                         return;
                 dibujaO();
                 turnoX=true;
                 revisarEstado();
             }
             });
        }
        
        public double getCenterX(){ //Tanto el método x como el y definen en donde a donde se dibujará la raya
            return getTranslateX()+100;
        }
         public double getCenterY(){
            return getTranslateY()+100;
        }
        public String getValor(){ //en este método se agarra el valor dentro de cada espacio del tablero
            return text.getText();
        }
        
        private void dibujaX(){
            text.setText("X");
        }
        
        private void dibujaO(){
            text.setText("O");
        }
        
    }
    public static void main(String[] args) {
      launch(args); //método que ejecutará el programa
    }
    
}
