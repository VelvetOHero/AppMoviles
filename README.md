# Description
This is the main code for an android app that calculates a tariff based on how much time a user spent on a parking. The user needs to enter the entry time and the leave time.
This uses simple if, else-if conditionals to work. The tariff is:
  - Less than 30 minutes is free,
  - Between 31 minutes and 60 minutes is fixed on $4.00
  - Between 61 and 120 is fixed on $12.00
  - Between 121 and 180 is fixed on $26.00

If the user stays longer than 3 hours, the tariff will work the next way:
  - For each hour, the tariff will be +14.00
  - For fractions, between 1 to 15 +4.00, between 16 to 30 +8.00, between 31 to 45 +11 and from 45 to the hour is the full +14.00.

The app also takes count on the possibility that the user entered a day before, so it counts the hours from one day to another.

It's not polished, but works. (I hope so)

# Main Activity
...Java
    
    package com.example.practica4;
    
    import static java.lang.String.format;
    
    import android.os.Bundle;
    import android.view.View;
    import android.widget.Button;
    import android.widget.EditText;
    import android.widget.TextView;
    
    import androidx.activity.EdgeToEdge;
    import androidx.appcompat.app.AppCompatActivity;
    import androidx.core.graphics.Insets;
    import androidx.core.view.ViewCompat;
    import androidx.core.view.WindowInsetsCompat;
    
    public class MainActivity extends AppCompatActivity {
    
        // Declaración de variables para los elementos de la interfaz de usuario
        Button boton;
        TextView test;
        TextView salida;
        EditText enHora;
        EditText enMin;
        EditText salHora;
        EditText salMin;
        // Variables para almacenar los tiempos de entrada y salida en minutos
        double tiempoE;
        double tiempoS;
    
        // Variable para almacenar el tiempo total (no utilizada actualmente en el código)
        int tiempo;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            // Habilita el modo EdgeToEdge para la actividad
            EdgeToEdge.enable(this);
            // Establece el diseño de la actividad
            setContentView(R.layout.activity_main);
            // Configura el listener para los insets de la ventana, ajustando el padding de la vista principal
            ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
                Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
                v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
                return insets;
            });
    
            // Inicialización de los elementos de la interfaz de usuario mediante sus IDs
            boton = findViewById(R.id.boton1);
            test = findViewById(R.id.TiempoEstimado);
            salida = findViewById(R.id.MontoFinal);
            enHora = findViewById(R.id.entradaHoras);
            enMin = findViewById(R.id.entradaMinutos);
            salHora = findViewById(R.id.salidaHoras);
            salMin = findViewById(R.id.salidaMinutos);
    
            // Configuración del listener para el evento onClick del botón
            boton.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    // Obtiene los valores de los EditText como cadenas de texto
                    String enhora = enHora.getText().toString();
                    String enmin = enMin.getText().toString();
                    String salhora = salHora.getText().toString();
                    String salmin = salMin.getText().toString();
    
                    // Verifica si los campos de hora y minutos de entrada no están vacíos
                    if (!enhora.isEmpty() && !enmin.isEmpty()) {
                        // Convierte las horas y minutos de entrada a enteros
                        int horas = Integer.parseInt(enhora);
                        int minutos = Integer.parseInt(enmin);
                        // Calcula el tiempo de entrada total en minutos
                        tiempoE = (horas * 60) + minutos;
                    }
                    // Verifica si los campos de hora y minutos de salida no están vacíos
                    if (!salhora.isEmpty() && !salmin.isEmpty()) {
                        // Convierte las horas y minutos de salida a enteros
                        int horas = Integer.parseInt(salhora);
                        int minutos = Integer.parseInt(salmin);
                        // Calcula el tiempo de salida total en minutos
                        tiempoS = (horas * 60) + minutos;
                    }
                    // Calcula la diferencia de tiempo total en minutos, considerando si la salida es al día siguiente
                    double mintot;
                    if (tiempoS < tiempoE) { // Si la hora de salida es menor que la de entrada (día siguiente)
                        mintot = (1440 - tiempoE) + tiempoS; // 1440 minutos en un día
                    } else {
                        mintot = tiempoS - tiempoE;
                    }
    
                    // Determina el costo del estacionamiento basado en el tiempo total
                    if (mintot < 30 ) {
                        // Si el tiempo es menor a 30 minutos, es gratis
                         salida.setText("Gratis");
                    } else if (mintot <= 60) {
                        // Si el tiempo es menor a 60 minutos (1 hora)
                        salida.setText("$4.00");
                    } else if (mintot <= 120) {
                        // Si el tiempo es menor a 120 minutos (2 horas)
                        salida.setText("$12.00");
    
                    } else if (mintot <= 180) {
                        // Si el tiempo es menor a 180 minutos (3 horas)
                        salida.setText("$26.00");
                    } else {
                        // A partir de la cuarta hora
                        double monto = 26.00; // Monto base hasta las 3 horas (180 minutos)
                        double minutosExcedentes = mintot - 180; // Minutos después de las 3 horas
    
                        // Calculamos cuántas horas completas adicionales hay
                        int horasCompletasAdicionales = (int) (minutosExcedentes / 60);
                        monto += horasCompletasAdicionales * 14; // Cada hora completa adicional cuesta $14
    
                        // Calculamos los minutos restantes después de las horas completas adicionales
                        double minutosFraccion = minutosExcedentes % 60;
    
                        // Calculamos el costo de la fracción de hora
                        if (minutosFraccion > 0) {
                            if (minutosFraccion <= 15) {
                                monto += 4;
                            } else if (minutosFraccion <= 30) {
                                monto += 8;
                            } else if (minutosFraccion <= 45) {
                                monto += 11;
                            } else { // minutosFraccion < 60
                                monto += 14;
                            }
                        }
    
                        salida.setText(String.format("$%.2f", monto));
                    }
                }
            });
        }
    }
...

# Layout
...XML

    <?xml version="1.0" encoding="utf-8"?>
    <androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <TableLayout
        android:id="@+id/table"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintTop_toTopOf="parent">

        <TableRow
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="#913CFF"
            android:orientation="horizontal"
            android:padding="12dp">

            <TextView
                android:text="Estacionamiento"
                android:textColor="@color/white"
                android:fontFamily="sans-serif-medium"
                android:textSize="17sp"
                />
        </TableRow>
    </TableLayout>


            <TextView
                android:id="@+id/Entrada"
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toBottomOf="@id/table"
                android:textColor="@color/black"
                android:textSize="15sp"
                android:text="Entrada HH:MM"
                android:padding="15dp"/>

            <EditText
                android:id="@+id/entradaHoras"
                android:layout_height="wrap_content"
                android:layout_width="50sp"
                app:layout_constraintStart_toEndOf="@id/Entrada"
                app:layout_constraintTop_toBottomOf="@id/table"
                android:layout_marginLeft="10dp"
                android:hint="HH" />

            <TextView
                android:id="@+id/separador"
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                app:layout_constraintStart_toEndOf="@id/entradaHoras"
                app:layout_constraintTop_toBottomOf="@id/table"
                android:textColor="@color/black"
                android:textSize="15sp"
                android:text=":"
                android:padding="15dp"/>

            <EditText
                android:id="@+id/entradaMinutos"
                android:layout_height="wrap_content"
                android:layout_width="50sp"
                app:layout_constraintStart_toEndOf="@id/separador"
                app:layout_constraintTop_toBottomOf="@id/table"
                android:hint="MM" />

            <TextView
                android:id="@+id/Salida"
                android:layout_height="wrap_content"
                android:layout_width="wrap_content"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toBottomOf="@id/Entrada"
                android:textColor="@color/black"
                android:textSize="15sp"
                android:text="Salida HH:MM"
                android:padding="15dp" />

            <EditText
                android:id="@+id/salidaHoras"
                android:layout_height="wrap_content"
                android:layout_width="50sp"
                app:layout_constraintStart_toEndOf="@id/Entrada"
                app:layout_constraintTop_toBottomOf="@id/entradaHoras"
                android:layout_marginLeft="10dp"
                android:hint="HH" />

    <TextView
        android:id="@+id/separador2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:text=":"
        android:textColor="@color/black"
        android:textSize="15sp"
        app:layout_constraintStart_toEndOf="@id/salidaHoras"
        app:layout_constraintTop_toBottomOf="@id/entradaHoras" />

    <EditText
                android:id="@+id/salidaMinutos"
                android:layout_height="wrap_content"
                android:layout_width="50sp"
                app:layout_constraintStart_toEndOf="@id/separador2"
                app:layout_constraintTop_toBottomOf="@id/entradaHoras"
                android:hint="MM" />

            <TextView
                android:id="@+id/TiempoEstimado"
                android:layout_height="wrap_content"
                android:layout_width="match_parent"
                app:layout_constraintTop_toBottomOf="@id/Salida"
                android:padding="30dp"
                android:text="Tiempo estimado"
                android:textAlignment="center"
                android:textColor="@color/black"
                android:textSize="15sp" />

            <TextView
                android:id="@+id/Monto"
                android:layout_height="wrap_content"
                android:layout_width="match_parent"
                app:layout_constraintTop_toBottomOf="@id/TiempoEstimado"
                android:textColor="#646464"
                android:textSize="15sp"
                android:text="Monto a pagar:"
                android:textAlignment="center" />

            <TextView
                android:id="@+id/MontoFinal"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                app:layout_constraintTop_toBottomOf="@id/Monto"
                android:text="$$$"
                android:textSize="30sp"
                android:textAlignment="center"
                android:layout_marginTop="20dp" />

            <Button
                android:id="@+id/boton1"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintTop_toBottomOf="@id/MontoFinal"
                android:layout_marginTop="100dp"
                android:text="CALCULAR"
                android:backgroundTint="@color/black"
                android:textColor="@color/white"
                android:textSize="12sp" />



                </androidx.constraintlayout.widget.ConstraintLayout>

...

