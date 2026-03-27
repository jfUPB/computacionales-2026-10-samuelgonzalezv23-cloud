# Unidad 5
## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

#### Actividad 5
Evidencia 1 
Breakpoint dentro de createRisingParticle() justo al crear la partícula:
```
particles.push_back(new SpiralParticle(pos, col));
```
Este punto permite inspeccionar el objeto recién creado y ver cómo se organiza la jerarquía en memoria.

<img width="787" height="603" alt="image" src="https://github.com/user-attachments/assets/8c04d0dc-c993-40a3-b384-d50c344528cc" />

<img width="909" height="217" alt="image" src="https://github.com/user-attachments/assets/eda3b4e5-1a82-4045-b4d6-886d9a95b7f1" />

al ser particle type 1, se sabe que es una spiral particle
se pueden ver lifetime y color heredados de Particle / ExplosionParticle 
se puede ver velocidad propia de SpiralParticle
Otras variables como age heredada de particle o angulo que es propia de spiral particle no se ven puesto que son inicializadas en cero.

Demuestra cómo la herencia estructura los objetos en memoria un solo bloque contiene los campos de todas las clases base y derivadas. Permite reutilizar atributos y extender funcionalidades.


## Bitácora de reflexión
