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
// -------------------------
// Inicializar variables
// -------------------------
@10
D=A
@0
M=D       // a = 10

@20
D=A
@1
M=D       // b = 20

// -------------------------
// Inicializar punteros
// -------------------------
@0
D=A
@R2
M=D       // pa = &a

@1
D=A
@R3
M=D       // pb = &b

// -------------------------
// swap(*pa, *pb)
// -------------------------

// tmp = *pa
@R2
A=M
D=M
@R4
M=D       // tmp = *pa

// *pa = *pb
@R3
A=M
D=M       // D = *pb
@R2
A=M
M=D       // *pa = *pb

// *pb = tmp
@R4
D=M       // D = tmp
@R3
A=M
M=D       // *pb = tmp

```

```
// arr[0] = 10
@10
D=A
@16
M=D

// arr[1] = 15
@15
D=A
@17
M=D

// arr[2] = 2
@2
D=A
@18
M=D

// arr[3] = 3
@3
D=A
@19
M=D

// arr[4] = 50
@50
D=A
@20
M=D

```

## Bitácora de reflexión

