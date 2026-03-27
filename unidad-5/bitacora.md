# Unidad 5
## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

#### Actividad 5

##### Códigos

Partícula zig zag
```
class SpiralParticle : public Particle {
private:
	glm::vec2 center;
	float radius;
	float angle;
	float speed;
	float riseSpeed;
	float lifetime;
	float age;
	ofColor color;
	bool exploded;

public:
	SpiralParticle(const glm::vec2 & start, const ofColor & col)
		: center(start)
		, radius(0)
		, angle(0)
		, speed(ofRandom(4, 8))
		, riseSpeed(ofRandom(80, 140))
		, lifetime(ofRandom(2, 4))
		, age(0)
		, color(col)
		, exploded(false) { }

	void update(float dt) override {
		age += dt;
		angle += speed * dt;
		radius += 20 * dt;

		center.y -= riseSpeed * dt;

		if (age >= lifetime || center.y < ofGetHeight() * 0.2f) {
			exploded = true;
		}
	}

	void draw() override {
		glm::vec2 pos = center + glm::vec2(cos(angle), sin(angle)) * radius;
		ofSetColor(color);
		ofDrawCircle(pos, 6);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return center; }
	ofColor getColor() const override { return color; }
};
```

partícula espiral
```
class ZigZagParticle : public Particle {
private:
	glm::vec2 position;
	float speedY;
	float amplitude;
	float frequency;
	float age;
	float lifetime;
	ofColor color;
	bool exploded;

public:
	ZigZagParticle(const glm::vec2 & start, const ofColor & col)
		: position(start)
		, speedY(ofRandom(120, 200))
		, amplitude(ofRandom(30, 80))
		, frequency(ofRandom(5, 10))
		, age(0)
		, lifetime(ofRandom(2, 4))
		, color(col)
		, exploded(false) { }

	void update(float dt) override {
		age += dt;

		position.y -= speedY * dt;
		position.x += sin(age * frequency) * amplitude * dt;

		if (age >= lifetime || position.y < ofGetHeight() * 0.2f) {
			exploded = true;
		}
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, 8);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return position; }
	ofColor getColor() const override { return color; }
};
```
Explosión circular
```
class RingExplosion : public ExplosionParticle {
public:
	RingExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.0f, 0) { }

	void update(float dt) override {
		age += dt;

		// El radio crece linealmente
		size += 200 * dt;

		// Fade simple
		color.a = ofMap(age, 0, lifetime, 255, 0, true);
	}

	void draw() override {
		ofNoFill();
		ofSetColor(color);
		ofDrawCircle(position, size);
		ofFill();
	}
};

```


##### Evidencias
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
