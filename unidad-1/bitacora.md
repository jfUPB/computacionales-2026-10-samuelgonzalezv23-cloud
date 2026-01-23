# Unidad 1

## Bitácora de proceso de aprendizaje

### Actividad 1

El código modifica los registros de la cpu unas veces y luego escribe un valor en la address 16 de la ram, finalmente termina su ejecución creando un loop indefinido de acciones que no hacen nada.
```
@1
D=A
@2
D=D+A
@16
M=D
@END
(END)
0;JMP
```
Las instrucciones realizan lo siguiente:

@1 puede modificar el valor del registro A, el registro A corresponde a la address en la ram a la que se va a escribir, en este caso el 1

D=A puede modificar el valor del registro D, el registro D se usa para guardar valores que queremos escribir en la ram, en este caso convertimos el valor de D al valor de A, siendo este 1.

@2 modificamos el valor de A a 2.

D=D+A modificamos el valor de D para que sea el actual valor de D + el valor de A, efectivamente le sumamos A, que en este punto es 2. Ahora D=3

@16 modificamos el valor de A a 16.

M=D Escribimos el valor de D en la memoria, la address que se está escribiendo en memoria corresponde al valor de A, que es 16, por lo que ahora el valor guardado en la address 16 de la ram es ahora 3.

@END Esto hace que el valor del registro A corresonda al número de línea de la línea de código apodadada (END) una vez el código sea traducido por la máquina

(END)
0;JMP   Esta es la línea de código apodada (END), en el código original, es la línea número 7, por lo que cuando vemos las instrucciones traducidas por la máquina, la línea anterior convierte el valor del registro A en 7. Esta línea de código tiene 2 partes, separadas por punto y coma; la primera es una operación nula y la segunda es JMP o Jump, que hace que el program counter salte a la posición igual al valor del registro A, que es 7, lo que causa que esta linea de código se repita en bucle de forma indefinida y termine la ejecución del programa.

### Actividad 2
```
@5
D=A
@10
D=D+A
@20
M=D
@END
(END)
0;JMP
```
## Bitácora de aplicación 



## Bitácora de reflexión



