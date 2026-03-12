# Unidad 4

## Bitácora de proceso de aprendizaje

crear una solución con openframeworks

## Bitácora de aplicación 

ofApp.h
```
#pragma once
#include "ofMain.h"

// Nodo de la cola
struct Node {
	float x, y;
	float radius;
	ofColor color;
	float opacity;
	Node * next;

	Node(float _x, float _y, float _radius, ofColor _color, float _opacity)
		: x(_x)
		, y(_y)
		, radius(_radius)
		, color(_color)
		, opacity(_opacity)
		, next(nullptr) {
	}
};

// Implementación manual de una cola (FIFO)
class BrushQueue {
public:
	Node * front;
	Node * rear;
	int size;
	int maxSize;

	BrushQueue(int _maxSize);
	~BrushQueue();

	void enqueue(float x, float y, float radius, ofColor color, float opacity);
	void dequeue();
	void clear();
	bool isEmpty();
};

// Constructor
BrushQueue::BrushQueue(int _maxSize)
	: front(nullptr)
	, rear(nullptr)
	, size(0)
	, maxSize(_maxSize) { }

// Destructor
BrushQueue::~BrushQueue() {
	clear();
}

// Implementa aquí `enqueue()`
void BrushQueue::enqueue(float x, float y, float radius, ofColor color, float opacity) {

	Node * newNode = new Node(x, y, radius, color, opacity);    //crea un nodo nuevo

	if (isEmpty()) {
		front = rear = newNode;                         //si la cola está vacía, tanto front como rear apuntan al nodo
	} else {
		rear->next = newNode;
		rear = newNode;                                 //si no, se conecta al final
	}

	size++;

	if (size > maxSize) {
		dequeue();                                         //si el tamaño sobrepasa el máximo, borra el nodo
	}
}

// Implementa aquí `dequeue()`
void BrushQueue::dequeue() {

	if (isEmpty()) return;                               //si la cola está vacía, no hacer nada

	Node * temp = front;                                 //guarda el primer nodo
	front = front->next;                                 //avanza front

	delete temp;                                         //libera memoria

	size--;

	if (front == nullptr) {                             //si la lista quedó vacía
		rear = nullptr;
	}
}

// Implementa aquí `clear()`
void BrushQueue::clear() {

	while (!isEmpty()) {                     //si la lista no está vacía, borra el último nodo y repite hasta que esté vacía
		dequeue();
	}
}

// Implementa aquí `isEmpty()`
bool BrushQueue::isEmpty() {

	return front == nullptr;
}

class ofApp : public ofBaseApp {
public:
	BrushQueue strokes; // Cola de trazos
	float backgroundHue = 0;

	ofApp()
		: strokes(50) { } // Tamaño máximo de la cola

	void setup();
	void update();
	void draw();
	void keyPressed(int key);
};

```

ofApp.cpp
```
#include "ofApp.h"

//--------------------------------------------------------------
void ofApp::setup() {
	ofBackground(0);
}

//--------------------------------------------------------------
void ofApp::update() {
	
	 backgroundHue += 0.2;
	if (backgroundHue > 255) backgroundHue = 0;

	if (ofGetMousePressed()) {

		float x = ofGetMouseX();
		float y = ofGetMouseY();

		float radius = ofRandom(5, 20);
		ofColor color = ofColor::fromHsb(ofRandom(255), 200, 255);

		float opacity = 200;

		strokes.enqueue(x, y, radius, color, opacity);
	}
}

//--------------------------------------------------------------
void ofApp::draw() {
	//fondo con gradiente dinámico
	ofColor color1, color2;
	color1.setHsb(backgroundHue, 150, 240);
	color2.setHsb(fmod(backgroundHue + 128, 255), 150, 240);
	ofBackgroundGradient(color1, color2, OF_GRADIENT_LINEAR);

	Node * current = strokes.front;

	while (current != nullptr) {

		ofSetColor(current->color, current->opacity);
		ofDrawCircle(current->x, current->y, current->radius);

		current = current->next;
	}
}

//--------------------------------------------------------------
void ofApp::keyPressed(int key) {
	if (key == 'c') {
		strokes.clear();
	}
	if (key == 'a') {

		if (strokes.maxSize == 50) {
			strokes.maxSize = 100;
		} else {
			strokes.maxSize = 50;
		}

	} else if (key == 's') {

		ofSaveScreen("painting_" + ofGetTimestampString() + ".png");
	}
}

```

<img width="619" height="313" alt="image" src="https://github.com/user-attachments/assets/2ae8febd-1222-4d5d-b844-5e8f64e0bd86" />
<img width="943" height="219" alt="image" src="https://github.com/user-attachments/assets/85c847e4-2b4e-4380-b1b3-9c90b13bff68" />








## Bitácora de reflexión

