# Unidad 1

## Bitácora de proceso de aprendizaje

### Actividad 1

El código modifica los registros de la cpu unas veces y luego escribe un valor en la address 16 de la ram, finalmente termina su ejecución creando un loop indefinido de acciones que no hacen nada.

@1
D=A
@2
D=D+A
@16
M=D
@END
(END)
0;JMP

Las instrucciones realizan lo siguiente:

@1 puede modificar el valor del registro A, el registro A corresponde a la address en la ram a la que se va a escribir, en este caso el 1

D=A puede modificar el valor del registro D, el registro D se usa para guardar valores que queremos escribir en la ram, en este caso convertimos el valor de D al valor de A, siendo este 1.

@2 modificamos el valor de A a 2.

D=D+A modificamos el valor de D para que sea el actual valor de D + el valor de A, efectivamente le sumamos A, que en este punto es 2.

## Bitácora de aplicación 



## Bitácora de reflexión
