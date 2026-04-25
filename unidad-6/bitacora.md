# Unidad 6

## Bitácora de proceso de aprendizaje

### Actividad 1

Presionar la tecla A atrae al as particulas al mouse
Presionar la tecla R repele las particulas del mouse
Presionar la tecla S para todas las particulas en el lugar
Presionar la tecla N la da a todas las partículas una velocidad y dirección aleatoria

Hay partículas de distintos colores, tamaños y velocidades. el tamaño y la velocidad ligeramente corresponden de manera inversa, puesto que las partículas de tamaño mayor también se mueven más despacio.

Hipótesis: Al presionar una tecla se cambia un estado global que cada particula observa y cambia su comportamiento acorde.

### Actividad 2

## Bitácora de aplicación 

offApp.h
```
#pragma once

#include "ofMain.h"
#include <string>
#include <vector>

class Observer {
public:
	virtual ~Observer() = default;
	virtual void onNotify(const std::string & event) = 0;
};

class Subject {
public:
	void addObserver(Observer * observer);
	void removeObserver(Observer * observer);

protected:
	void notify(const std::string & event);

private:
	std::vector<Observer *> observers;
};

class Particle;

class State {
public:
	virtual ~State() = default;
	virtual void update(Particle * particle) = 0;
	virtual void onEnter(Particle * particle) { }
	virtual void onExit(Particle * particle) { }
};

class Particle : public Observer {
public:
	Particle();
	~Particle() override;

	Particle(const Particle &) = delete;
	Particle & operator=(const Particle &) = delete;

	void update();
	void draw();
	void onNotify(const std::string & event) override;

	void setState(State * newState);

	ofVec2f position;
	ofVec2f velocity;
	float size;
	ofColor color;

private:
	void keepInsideWindow();
	State * state;
};

class NormalState : public State {
public:
	void update(Particle * particle) override;
	void onEnter(Particle * particle) override;
};

class AttractState : public State {
public:
	void update(Particle * particle) override;
};

class RepelState : public State {
public:
	void update(Particle * particle) override;
};

class StopState : public State {
public:
	void update(Particle * particle) override;
};

class SpiralState : public State {
public:
	float offset = 0;
	float fastcos(float);
	void update(Particle * particle) override;
};


class ParticleFactory {
public:
	static Particle * createParticle(const std::string & type);
};

class ofApp : public ofBaseApp, public Subject {
public:
	~ofApp() override;
	void setup() override;
	void update() override;
	void draw() override;
	void keyPressed(int key) override;

private:
	std::vector<Particle *> particles;
};

```

offApp.cpp
```
#include "ofApp.h"
#include <algorithm>

void Subject::addObserver(Observer * observer) {
	if (!observer) return;
	if (std::find(observers.begin(), observers.end(), observer) == observers.end()) {
		observers.push_back(observer);
	}
}

void Subject::removeObserver(Observer * observer) {
	if (!observer) return;
	observers.erase(std::remove(observers.begin(), observers.end(), observer), observers.end());
}

void Subject::notify(const std::string & event) {
	for (Observer * observer : observers) {
		observer->onNotify(event);
	}
}

Particle::Particle()
	: state(nullptr) {
	position = ofVec2f(ofRandomWidth(), ofRandomHeight());
	velocity = ofVec2f(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
	size = ofRandom(2.0f, 5.0f);
	color = ofColor(255);

	state = new NormalState();
	state->onEnter(this);
}

Particle::~Particle() {
	if (state) {
		state->onExit(this);
		delete state;
		state = nullptr;
	}
}

void Particle::setState(State * newState) {
	if (state) {
		state->onExit(this);
		delete state;
	}
	state = newState;
	if (state) {
		state->onEnter(this);
	}
}

void Particle::update() {
	if (state) {
		state->update(this);
	}
	keepInsideWindow();
}

void Particle::draw() {
	ofPushStyle();
	ofSetColor(color);
	ofDrawCircle(position, size);
	ofPopStyle();
}

void Particle::onNotify(const std::string & event) {
	if (event == "attract") {
		setState(new AttractState());
	} else if (event == "repel") {
		setState(new RepelState());
	} else if (event == "stop") {
		setState(new StopState());
	} else if (event == "normal") {
		setState(new NormalState());
	} else if (event == "spiral") {
		setState(new SpiralState());
	}
}

void Particle::keepInsideWindow() {
	const float W = static_cast<float>(ofGetWidth());
	const float H = static_cast<float>(ofGetHeight());

	if (position.x < 0.0f) {
		position.x = 0.0f;
		velocity.x *= -1.0f;
	} else if (position.x > W) {
		position.x = W;
		velocity.x *= -1.0f;
	}
	if (position.y < 0.0f) {
		position.y = 0.0f;
		velocity.y *= -1.0f;
	} else if (position.y > H) {
		position.y = H;
		velocity.y *= -1.0f;
	}
}

void NormalState::onEnter(Particle * particle) {
	particle->velocity.set(ofRandom(-0.5f, 0.5f), ofRandom(-0.5f, 0.5f));
}

void NormalState::update(Particle * particle) {
	particle->position += particle->velocity;
}

static void steer(Particle * particle, const ofVec2f & toward, float accel, float vmax, float posScale) {
	ofVec2f dir = toward - particle->position;
	float len = dir.length();
	if (len > 1e-6f) {
		dir /= len;
		particle->velocity += dir * accel;
	}
	particle->velocity.limit(vmax);
	particle->position += particle->velocity * posScale;
}

void AttractState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	steer(particle, mouse, 0.05f, 3.0f, 0.2f);
}

float SpiralState :: fastcos(float x) {
	x += 1.57079632f;
	if (x > 3.14159265f) x -= 6.28318531f;

	const float B = 4.0f / 3.14159265f;
	const float C = -4.0f / (3.14159265f * 3.14159265f);

	float y = B * x + C * x * std::abs(x);

	return y;
}

void SpiralState::update(Particle * particle) {
	offset = offset + 0.01;
	if (offset > 6.28318531f) offset -= 6.28318531f;
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	steer(particle, mouse, 0.05f, 3.0f, 0.05+ fastcos(offset)*0.7);
}

void RepelState::update(Particle * particle) {
	ofVec2f mouse(ofGetMouseX(), ofGetMouseY());
	ofVec2f away = particle->position - mouse;
	float len = away.length();
	if (len > 1e-6f) {
		away /= len;
		particle->velocity += away * 0.05f;
	}
	particle->velocity.limit(3.0f);
	particle->position += particle->velocity * 0.2f;
}

void StopState::update(Particle * particle) {
	particle->velocity *= 0.80f;
	if (particle->velocity.lengthSquared() < 1e-4f) {
		particle->velocity.set(0.0f, 0.0f);
	}
	particle->position += particle->velocity;
}

Particle * ParticleFactory::createParticle(const std::string & type) {
	Particle * particle = new Particle();
	if (type == "star") {
		particle->size = ofRandom(2.0f, 4.0f);
		particle->color = ofColor(255, 0, 0);
	} else if (type == "shooting_star") {
		particle->size = ofRandom(3.0f, 6.0f);
		particle->color = ofColor(0, 255, 0);
		particle->velocity *= 3.0f;
	} else if (type == "planet") {
		particle->size = ofRandom(5.0f, 8.0f);
		particle->color = ofColor(0, 0, 255);
	} else if (type == "giant") {
		particle->size = ofRandom(15.0f, 20.0f);
		particle->color = ofColor(160, 40, 160);
		particle->velocity *= 0.2f;
	}
	return particle;
}

ofApp::~ofApp() {
	for (Particle * p : particles) {
		removeObserver(p);
		delete p;
	}
	particles.clear();
}

void ofApp::setup() {
	ofBackground(0);
	particles.reserve(100 + 5 + 10);

	for (int i = 0; i < 100; ++i) {
		Particle * p = ParticleFactory::createParticle("star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 5; ++i) {
		Particle * p = ParticleFactory::createParticle("shooting_star");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 10; ++i) {
		Particle * p = ParticleFactory::createParticle("planet");
		particles.push_back(p);
		addObserver(p);
	}
	for (int i = 0; i < 3; ++i) {
		Particle * p = ParticleFactory::createParticle("giant");
		particles.push_back(p);
		addObserver(p);
	}
}

void ofApp::update() {
	for (Particle * p : particles) {
		p->update();
	}
}

void ofApp::draw() {
	for (Particle * p : particles) {
		p->draw();
	}
}

void ofApp::keyPressed(int key) {
	switch (key) {
	case 's':
		notify("stop");
		break;
	case 'a':
		notify("attract");
		break;
	case 'r':
		notify("repel");
		break;
	case 'n':
		notify("normal");
		break;
	case 'g':
		notify("spiral");
		break;
	default:
		break;
	}
}

```

#### Evidencia 1 — Nueva partícula en la Factory (Giant)
Breakpoint en ParticleFactory::createParticle sobre la línea else if (type == "giant").
<img width="794" height="348" alt="image" src="https://github.com/user-attachments/assets/0b546f36-b2ee-47ad-8562-a5db64a99855" />

El depurador confirma que el flujo entra en la rama específica del condicional basada en el string enviado.
Demuestra la especialización del objeto. El Factory encapsula la configuración, evitando que los parametros más específicos de la construcción como tamaño y color se hagan dentro de ofapp.h.
<img width="1254" height="118" alt="image" src="https://github.com/user-attachments/assets/fc3562f5-9447-452f-a564-03e2bf685070" />


#### Evidencia 2 — Nuevo estado en la vtable (SpiralState)
Breakpoint dentro de Particle::update(), inspeccionando el puntero state.
<img width="336" height="111" alt="image" src="https://github.com/user-attachments/assets/769f744b-0345-441d-a566-96479711337d" />

Se observa que la dirección de memoria en la tabla de funciones virtuales cambia al ejecutar setState.
Esto evidencia el despacho dinámico. El programa no sabe qué update ejecutar hasta el tiempo de ejecución, donde consulta la vtable del objeto actual para resolver el polimorfismo.
<img width="1224" height="176" alt="image" src="https://github.com/user-attachments/assets/85a948bb-6c99-45aa-8ebe-d26094739c02" />


#### Evidencia 3 — Cadena Observer → State completa
Breakpoint en Particle::setState tras presionar la tecla 'g'.
<img width="437" height="181" alt="image" src="https://github.com/user-attachments/assets/86a9bc8e-34bd-4f85-8345-bd2fbbcbc87f" />

La pila de llamadas muestra la secuencia: ofApp::keyPressed → Subject::notify → Particle::onNotify → Particle::setState.
La pila muestra cómo un evento de teclado se propaga a través del Subject hacia todos los Observers.
Se notifica una intención ("spiral") y cada Observer reacciona de forma autónoma cambiando su estado interno.
<img width="403" height="126" alt="image" src="https://github.com/user-attachments/assets/1d25c76c-6aba-45dd-8e00-6437d2bbc306" />


#### Evidencia 4 — Decisión de diseño: Encapsulamiento en SpiralState

Implementé mi propia función fastcos para no sobrecargar el programa con calculos innecesariamente precisos. para hacer funcionar la oscilación solo requería de una función periódica para que los valores oscilaran entre positivo y negativo. una aproximación de coseno es más que suficiente.
Esta aproximación de coseno se rompe una vez los valores se vuelven altos, por esto mismo dentro de spiral update el valor del offset se resetea a aproximadamente -2 pi una vez alcanza aproximadamente 2 pi.

Incluir la lógica de oscilación (fastcos y offset) exclusivamente dentro de SpiralState en lugar de en la clase base Particle.
Breakpoint en el método SpiralState::update.
<img width="670" height="334" alt="image" src="https://github.com/user-attachments/assets/f77cce38-d88b-4b67-aead-fee27fca5f6b" />

El offset incrementa solo en este estado, mientras que en otros estados esa variable no existe o no se altera.
Se aplica el principio de Responsabilidad Única. Al no contaminar la clase Particle con variables que solo usa un movimiento específico, mantenemos la memoria de las partículas ligera (mínimo payload) y facilitamos la creación de nuevos estados sin modificar el núcleo del sistema.
<img width="1137" height="82" alt="image" src="https://github.com/user-attachments/assets/f0ce034d-307b-4f39-8986-665ade0df5b7" />

## Bitácora de reflexión
