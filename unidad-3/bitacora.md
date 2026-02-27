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




El segundo problema es que la copia 
```
Personaje copiaHeroe = heroe;
```
Es una copia superficial que no crea un array con valores nuevos

## Bitácora de reflexión
