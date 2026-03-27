# Unidad 5
## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

#### Actividad 5

##### Códigos nuevos

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

##### Código entero

OfApp.h
```
#pragma once
#include "ofMain.h"
#include <vector>

// -------------------------------------------------
// Clase base abstracta: Particle
// -------------------------------------------------
class Particle {
public:
	virtual ~Particle() { }
	virtual void update(float dt) = 0;
	virtual void draw() = 0;
	virtual bool isDead() const = 0;
	virtual bool shouldExplode() const { return false; }
	virtual glm::vec2 getPosition() const { return glm::vec2(0, 0); }
	virtual ofColor getColor() const { return ofColor(255); }
};

// -------------------------------------------------
// RisingParticle: Partícula que nace en la parte inferior y sube
// -------------------------------------------------
class RisingParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float lifetime;
	float age;
	bool exploded;

public:
	RisingParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life)
		: position(pos)
		, velocity(vel)
		, color(col)
		, lifetime(life)
		, age(0)
		, exploded(false) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		velocity.y += 9.8f * dt * 8;
		float explosionThreshold = ofGetHeight() * 0.15f + ofRandom(-30, 30);
		if (position.y <= explosionThreshold || age >= lifetime) {
			exploded = true;
		}
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, 10);
	}

	bool isDead() const override { return exploded; }
	bool shouldExplode() const override { return exploded; }
	glm::vec2 getPosition() const override { return position; }
	ofColor getColor() const override { return color; }
};

// -------------------------------------------------
// SpiralParticle: sube en espiral
// -------------------------------------------------
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

// -------------------------------------------------
// ZigZagParticle: movimiento en zig-zag
// -------------------------------------------------
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

// -------------------------------------------------
// Clase base para explosiones: ExplosionParticle
// -------------------------------------------------
class ExplosionParticle : public Particle {
protected:
	glm::vec2 position;
	glm::vec2 velocity;
	ofColor color;
	float age;
	float lifetime;
	float size;

public:
	ExplosionParticle(const glm::vec2 & pos, const glm::vec2 & vel,
		const ofColor & col, float life, float sz)
		: position(pos)
		, velocity(vel)
		, color(col)
		, age(0)
		, lifetime(life)
		, size(sz) { }

	void update(float dt) override {
		position += velocity * dt;
		age += dt;
		float alpha = ofMap(age, 0, lifetime, 255, 0, true);
		color.a = alpha;
	}

	bool isDead() const override { return age >= lifetime; }
};

// -------------------------------------------------
// CircularExplosion: Explosión en patrón circular
// -------------------------------------------------
class CircularExplosion : public ExplosionParticle {
public:
	CircularExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.2f, ofRandom(16, 32)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(80, 200);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		ofDrawCircle(position, size);
	}
};

// -------------------------------------------------
// RandomExplosion: Explosión con direcciones aleatorias
// -------------------------------------------------
class RandomExplosion : public ExplosionParticle {
public:
	RandomExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.5f, ofRandom(16, 32)) {
		velocity = glm::vec2(ofRandom(-200, 200), ofRandom(-200, 200));
	}

	void draw() override {
		ofSetColor(color);
		ofDrawRectangle(position.x, position.y, size, size);
	}
};

// -------------------------------------------------
// StarExplosion: Explosión en forma de estrella
// -------------------------------------------------
class StarExplosion : public ExplosionParticle {
public:
	StarExplosion(const glm::vec2 & pos, const ofColor & col)
		: ExplosionParticle(pos, glm::vec2(0, 0), col, 1.3f, ofRandom(20, 40)) {
		float angle = ofRandom(0, TWO_PI);
		float speed = ofRandom(90, 180);
		velocity = glm::vec2(cos(angle), sin(angle)) * speed;
	}

	void draw() override {
		ofSetColor(color);
		int rays = 5;
		float outerRadius = size;
		float innerRadius = size * 0.5f;
		ofPushMatrix();
		ofTranslate(position);
		for (int i = 0; i < rays; i++) {
			float theta = ofMap(i, 0, rays, 0, TWO_PI);
			float xOuter = cos(theta) * outerRadius;
			float yOuter = sin(theta) * outerRadius;
			float xInner = cos(theta + PI / rays) * innerRadius;
			float yInner = sin(theta + PI / rays) * innerRadius;
			ofDrawLine(0, 0, xOuter, yOuter);
			ofDrawLine(xOuter, yOuter, xInner, yInner);
		}
		ofPopMatrix();
	}
};



// -------------------------------------------------
// RingExplosion: circunferencia  que crece
// -------------------------------------------------
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

// -------------------------------------------------
// ofApp: Manejo de la escena y eventos
// -------------------------------------------------
class ofApp : public ofBaseApp {
public:
	void setup();
	void update();
	void draw();
	void mousePressed(int x, int y, int button);
	void keyPressed(int key);

	std::vector<Particle *> particles;
	~ofApp();

private:
	void createRisingParticle();
};

```

OfApp.cpp
```
#include "ofApp.h"

// --------------------------------------------------------------
void ofApp::setup() {
	ofSetFrameRate(60);
	ofBackground(0);
}

// --------------------------------------------------------------
void ofApp::update() {
	float dt = ofGetLastFrameTime();

	for (int i = 0; i < particles.size(); i++) {
		particles[i]->update(dt);
	}

	for (int i = particles.size() - 1; i >= 0; i--) {
		if (particles[i]->shouldExplode()) {
			int explosionType = (int)ofRandom(4);
			int numParticles = (int)ofRandom(20, 30);
			for (int j = 0; j < numParticles; j++) {
				if (explosionType == 0) {
					particles.push_back(new CircularExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 1) {
					particles.push_back(new RandomExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				} else if (explosionType == 3){
					particles.push_back(new StarExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				}else {
					particles.push_back(new RingExplosion(
						particles[i]->getPosition(), particles[i]->getColor()));
				}
			}
			delete particles[i];
			particles.erase(particles.begin() + i);
		} else if (particles[i]->isDead()) {
			delete particles[i];
			particles.erase(particles.begin() + i);
		}
	}
}

// --------------------------------------------------------------
void ofApp::draw() {
	for (int i = 0; i < particles.size(); i++) {
		particles[i]->draw();
	}
}

// --------------------------------------------------------------
void ofApp::createRisingParticle() {
	float minX = ofGetWidth() * 0.35f;
	float maxX = ofGetWidth() * 0.65f;
	float spawnX = ofRandom(minX, maxX);
	glm::vec2 pos(spawnX, ofGetHeight());

	glm::vec2 target(ofGetWidth() / 2.0f + ofRandom(-300, 300),
		ofGetHeight() * 0.10f + ofRandom(-30, 30));
	glm::vec2 direction = glm::normalize(target - pos);
	glm::vec2 vel = direction * ofRandom(250, 350);

	ofColor col;
	col.setHsb(ofRandom(255), 220, 255);

	float lifetime = ofRandom(1.5f, 3.5f);

	int particleType = (int)ofRandom(3); 

	if (particleType == 0) {
		//  original
		particles.push_back(new RisingParticle(pos, vel, col, lifetime));
	} else if (particleType == 1) {
		// Nuevo tipo 
		particles.push_back(new SpiralParticle(pos, col));
	} else {
		// Nuevo tipo 
		particles.push_back(new ZigZagParticle(pos, col));
	}
}

// --------------------------------------------------------------
void ofApp::mousePressed(int x, int y, int button) {
	createRisingParticle();
}

// --------------------------------------------------------------
void ofApp::keyPressed(int key) {
	if (key == ' ') {
		for (int i = 0; i < 1000; i++) {
			createRisingParticle();
		}
	}
	if (key == 's') {
		ofSaveScreen("screenshot_" + ofToString(ofGetFrameNum()) + ".png");
	}
}

// --------------------------------------------------------------
ofApp::~ofApp() {
	for (int i = 0; i < particles.size(); i++) {
		delete particles[i];
	}
	particles.clear();
}

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

Evidencia 2 

Breakpoint en update() de Draw:
```
particles[i]->update(dt);
```
<img width="567" height="93" alt="image" src="https://github.com/user-attachments/assets/3d5ac053-93a0-45a4-9da7-0662b8c334db" />



Muestra _vptr apuntando a la _vtable de SpiralParticle y _vtable de CircularExplosion.

<img width="1259" height="279" alt="image" src="https://github.com/user-attachments/assets/b2aaa88f-c2aa-4a40-b7ce-7950034d59c4" />

<img width="1263" height="278" alt="image" src="https://github.com/user-attachments/assets/f058ce60-2451-4643-b51e-26176e97e886" />


3. Explicación

Ambas tablas tienen entradas para update(), draw(), isDead(), etc.
Sin embargo, las direcciones de update() y draw() son distintas, apuntando a implementaciones específicas de cada clase.

4. Justificación

Demuestra cómo se implementa el polimorfismo: aunque los punteros son de tipo Particle*, el despacho dinámico usa la _vtable correcta para ejecutar los métodos específicos.


## Bitácora de reflexión
