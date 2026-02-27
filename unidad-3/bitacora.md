# Unidad 3

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

### Actividad

El primer problema es que el constructor de personaje le brinda a cada personaje un array nuevo ubicado en el heap por medio de new. Esto es un problema porque nunca lo borra incluso cuando el resto del personaje es borrado correctamente del stack.
<img width="662" height="502" alt="image" src="https://github.com/user-attachments/assets/284eb8ae-042d-4294-a94e-4ae38473de87" />

para probar esto, cree un while true que repite constantemente el encuentro y el programa no lo soporta, es un memory leak ya que todos los personajes crean un array nuevo que no borran.

<img width="306" height="172" alt="image" src="https://github.com/user-attachments/assets/af61f551-e01c-4dd0-b489-aade14276f55" />

Se puede ver como el uso de memoria crece constantemente sin bajar.

<img width="1036" height="876" alt="image" src="https://github.com/user-attachments/assets/24c3259d-d5ad-42db-9f04-b59dbabe678a" />

esto se soluciona creando un método destructor, en c++ nombrado igual que la clase pero con ~ al principio. este se ejecuta automáticamente cuando el objeto salga del scope y liberará el espacio del heap.
<img width="642" height="401" alt="image" src="https://github.com/user-attachments/assets/c9b804ae-7c40-4faa-a096-228ac3806558" />

la memoria se mantiene constante.




El segundo problema es que la copia 
```
Personaje copiaHeroe = heroe;
```
Es una copia superficial que no crea un array con valores nuevos.

esto se puede ver al editar las estadisticas de la copia, también modifica las estadísticas del original:
<img width="646" height="208" alt="image" src="https://github.com/user-attachments/assets/76701900-92af-400b-b8df-d8c506a6c392" />
<img width="567" height="72" alt="image" src="https://github.com/user-attachments/assets/8f4185e2-15cc-434e-b616-7b8f49e4115c" />

esto lo soluciono creando un constructor de copias con estadísticas nuevas
<img width="577" height="300" alt="image" src="https://github.com/user-attachments/assets/e81854f9-58a2-424e-8e7f-be41231572bb" />
ahora simular encuentro se ve así
<img width="707" height="410" alt="image" src="https://github.com/user-attachments/assets/aa045741-5c79-423c-85ea-4f45ef52cb1f" />
Y ahora el original y la copia pueden tener estadísticas distintas
<img width="736" height="216" alt="image" src="https://github.com/user-attachments/assets/84f1baee-2a84-46e1-ad01-1761e9b47112" />






## Bitácora de reflexión



