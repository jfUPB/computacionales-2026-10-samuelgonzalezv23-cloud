# Unidad 2

## Bitácora de proceso de aprendizaje

### Actividad 1
Dibujar un punto en la pantalla

```
// Dibujar un punto negro en la esquina superior izquierda

@SCREEN     // 16384
M=1       

(END)
@END
0;JMP

```
### Actividad 2
Dibujar una linea de 16 pixeles
Cada dirección de screen representa 10 pixeles
Escribir -1 guarda 1111111111111111
```
@SCREEN     // Dirección 16384
M=-1        // 1111111111111111

(END)
@END
0;JMP

```

### Actividad 3
```
// ------------------------------------
// Inicialización
// ------------------------------------

@0
D=A
@R0
M=D        // posición = 0

// ------------------------------------
// LOOP PRINCIPAL
// ------------------------------------

(LOOP)

// Limpiar pantalla (primer word)
@SCREEN
M=0

// Construir máscara desplazada
@1
D=A
@R1
M=D        // máscara base = 1

@R0
D=M
@SHIFTCOUNT
M=D        // contador = posición

(SHIFTLOOP)
@SHIFTCOUNT
D=M
@DRAW
D;JEQ

@R1
M=M+M      // desplazamiento izquierda (shift <<)

@SHIFTCOUNT
M=M-1
@SHIFTLOOP
0;JMP

(DRAW)
@R1
D=M
@SCREEN
M=D        // dibujar

// ------------------------------------
// Leer teclado
// ------------------------------------

@KBD
D=M

@100       // 'd'
D=D-A
@RIGHT
D;JEQ

@KBD
D=M
@105       // 'i'
D=D-A
@LEFT
D;JEQ

@LOOP
0;JMP

// ------------------------------------
// Mover derecha (shift >>)
// ------------------------------------

(RIGHT)
@R0
M=M-1
@LOOP
0;JMP

// ------------------------------------
// Mover izquierda (shift <<)
// ------------------------------------

(LEFT)
@R0
M=M+1
@LOOP
0;JMP

```




### Actividad 4

### Actividad 5
```
// int a = 10;
@10
D=A
@R0
M=D

// int* p; p = &a;
@R0
D=A
@R1
M=D

// *p = 20;
@R1
A=M
M=20

```
## Bitácora de aplicación 
### Actividad 8
```

(start)


//asignar a
@20
D=A
@a
M=D

//asignar b
@10
D=A
@b
M=D

//crear un puntero a a
@a
D=A
@pa
M=D

//crear un puntero a b
@b
D=A
@pb
M=D

//llamar la función swap
@swap
0;JMP

//fin del código
@end
(end)
0;JMP

//función swap
(swap)

//tmp = *pa
@pa
A=M
D=M
@tmp
M=D

// *pa = *pb

@pb
A=M
D=M
@pa
A=M
M=D

//*pb = tmp

@tmp
A=M
D=M
@pb
A=M
M=D


0;JMP

```
<img width="1037" height="607" alt="image" src="https://github.com/user-attachments/assets/b983e3b9-d28a-4b1f-9f5f-c3e5fc4378e7" />
Creamos variables a y b y creamos dos punteros pa y pb que apuntan hacia ellas

```
(start)

//inicializar array

// arr[0] = 10
@10
D=A
@R0
M=D

// arr[1] = 15
@15
D=A
@R1
M=D

// arr[2] = 2
@2
D=A
@R2
M=D

// arr[3] = 3
@3
D=A
@R3
M=D

// arr[4] = 50
@50
D=A
@R4
M=D

//inicializar variables

//int* parr
@R0
D=A
@parr
M=D

//int arrSize
@5
D=A
@arrsize
M=D

(main)

//llamar función
@calsum
0;JMP

@end
(end)
0;JMP

//función calsum
(calsum)

// int sum = 0
@0
D=A
@sum
M=D

//int i= 0
@0
D=A
@i
M=D

(calsumloop)

//*(parr+i)
@i
D=M
@parr
A=D+M
D=M

//sum = sum + *(parr+i)
@sum
M=D+M

//i++
@1
D=A
@i
M=D+M

//For i < arrSize
@arrsize
D=M
@i
D=D-M
@calsumloop
D;JNE


@end
0;JMP



```

## Bitácora de reflexión




