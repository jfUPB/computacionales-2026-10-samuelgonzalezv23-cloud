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

Evidencia 1: Nodo en cola vacía
<img width="845" height="322" alt="image" src="https://github.com/user-attachments/assets/aaecb468-cd88-4e65-b56f-4424a819eda8" />
<img width="908" height="228" alt="image" src="https://github.com/user-attachments/assets/7eaab8f2-3d06-4ff1-8183-d430271d0410" />


Evidencia 2: Sistema FIFO al insertar más nodos. 
<img width="821" height="311" alt="image" src="https://github.com/user-attachments/assets/3356ff43-a7ab-4299-80e5-8f6448837e4b" />
<img width="913" height="226" alt="image" src="https://github.com/user-attachments/assets/2557af68-06e9-4f50-9c1a-882b61c27b60" />


Evidencia 3: dequeue.

<img width="600" height="278" alt="image" src="https://github.com/user-attachments/assets/6d62b4e0-54e2-46af-be1f-17a9caba2c6b" />

dequeue guarda el nodo que está en frente por medio de un puntero, luego coloca un nuevo modo en el frente y luego borra el nodo al que está apuntando el puntero para liberar la memoria.

<img width="911" height="228" alt="image" src="https://github.com/user-attachments/assets/84d065d5-c4a4-4d88-ae0e-adbbbb5c9a0e" />


Evidencia 4: Control del máximo de la cola.
<img width="833" height="306" alt="image" src="https://github.com/user-attachments/assets/1cfb618e-44ee-4c20-a08f-26b425878e9b" />

De hecho, en mi código, dequeue solo se ejecuta dentro de enqueue una vez se sobrepasa el tamaño máximo.

Evidencia 5: Draw

<img width="592" height="289" alt="image" src="https://github.com/user-attachments/assets/53d8a4bc-ec0b-4734-a894-afe176e34a75" />

Draw dibujará cada nodo en la cola hasta que se encientre con un puntero nulo.

<img width="318" height="89" alt="image" src="https://github.com/user-attachments/assets/84680404-b818-45ed-92d0-53da054c4545" />

Y cuando la cola esté vacía, Front será nulo, ahí deja de dibujar.

<img width="910" height="228" alt="image" src="https://github.com/user-attachments/assets/00c5789a-1f77-42ce-86dc-17a2be05d02c" />



Evidencia 6: Clear
<img width="931" height="103" alt="image" src="https://github.com/user-attachments/assets/b49b1e49-c442-44a8-bceb-59d78da787d0" />

al llamar clear, mientras la lista no esté vacía, llamará continuamente dequeue para limpiar la cola.

<img width="913" height="224" alt="image" src="https://github.com/user-attachments/assets/3e94a0c4-b73f-4673-b6b3-784b7509e1b4" />





















## Bitácora de reflexión


