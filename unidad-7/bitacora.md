# Unidad 7

## Bitácora de proceso de aprendizaje

### Actividad 1
<img width="891" height="711" alt="image" src="https://github.com/user-attachments/assets/7a3921f0-2664-4036-aa62-99ce7dc367bb" />

¿como se ajusta automaticamente el triangulo al tamaño de la ventana?




## Bitácora de aplicación 

### Actividad 6

```
#include <iostream>
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include <ctime>   // Para la semilla del generador aleatorio time()
#include <cmath>   // Para funciones matemáticas como cos() y sin()

// --- CONFIGURACIÓN INICIAL ---
const unsigned int SCR_WIDTH = 800;  // Ancho inicial de la ventana
const unsigned int SCR_HEIGHT = 600; // Alto inicial de la ventana

// --- VARIABLES DE ESTADO ---
float posX = 400.0f, posY = 300.0f;           // Posición actual en PÍXELES (centro de la ventana)
float velX = 4.0f, velY = 3.0f;               // Velocidad de movimiento en PÍXELES por frame
float r = 1.0f, g = 0.5f, b = 0.2f;           // Color actual del triángulo (RGB)
int curWidth = SCR_WIDTH, curHeight = SCR_HEIGHT; // Tamaño actual de la ventana (se actualiza al redimensionar)
bool spacePressed = false;                    // Control para que la tecla Espacio no se repita infinitamente

// Función para asignar colores RGB aleatorios
void setRandomRGB() {
    r = (rand() % 100) / 100.0f; // Valor entre 0.0 y 1.0
    g = (rand() % 100) / 100.0f;
    b = (rand() % 100) / 100.0f;
}

// Función para cambiar la dirección a un ángulo aleatorio
void setRandomDirection() {
    float angle = static_cast<float>(rand() % 360) * 3.14159f / 180.0f; // Grados a Radianes
    float speed = 5.0f; // Velocidad constante en píxeles
    velX = cos(angle) * speed; // Componente horizontal del movimiento
    velY = sin(angle) * speed; // Componente vertical del movimiento
}

// Callback: Se ejecuta cuando el usuario cambia el tamaño de la ventana con el mouse
void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
    glViewport(0, 0, width, height); // Ajusta el área de dibujo de OpenGL
    curWidth = width;   // Actualiza nuestra variable de ancho para que el rebote sea real
    curHeight = height; // Actualiza nuestra variable de alto para que el rebote sea real
}

// Procesa la entrada de teclado
void processInput(GLFWwindow* window) {
    if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) // Cerrar con ESC
        glfwSetWindowShouldClose(window, true);

    // Lógica para la tecla ESPACIO (Cambio de dirección y color)
    if (glfwGetKey(window, GLFW_KEY_SPACE) == GLFW_PRESS) {
        if (!spacePressed) { // Si es la primera vez que se detecta el pulso
            setRandomRGB();
            setRandomDirection();
            spacePressed = true; // Bloqueamos hasta que se suelte la tecla
        }
    }
    if (glfwGetKey(window, GLFW_KEY_SPACE) == GLFW_RELEASE) {
        spacePressed = false; // Permitimos otra pulsación al soltar
    }
}

// --- SHADERS ---
const char* vertexShaderSrc = R"glsl(
    #version 460 core
    layout(location = 0) in vec3 aPos; // Posición local del triángulo (en píxeles)
    uniform vec2 screenRes;            // Resolución actual de la ventana (ancho, alto)
    uniform vec2 pixelPos;             // Posición deseada en píxeles

    void main() {
        // PASO 1: Sumar la posición actual a los vértices del triángulo
        vec2 positionInPixels = aPos.xy + pixelPos;
        // PASO 2: Convertir de rango [0, Res] a [0, 1]
        vec2 zeroToOne = positionInPixels / screenRes;
        // PASO 3: Convertir de rango [0, 1] a [0, 2]
        vec2 zeroToTwo = zeroToOne * 2.0;
        // PASO 4: Convertir de rango [0, 2] a [-1, 1] (Clip Space de OpenGL)
        vec2 clipSpace = zeroToTwo - 1.0;

        gl_Position = vec4(clipSpace, aPos.z, 1.0);
    }
)glsl";

const char* fragmentShaderSrc = R"glsl(
    #version 460 core
    out vec4 FragColor;
    uniform vec3 triangleColor; // Color enviado desde C++
    void main() {
        FragColor = vec4(triangleColor, 1.0);
    }
)glsl";

unsigned int VAO, VBO, shaderProg;

// Función para compilar y crear el programa de Shaders
unsigned int buildShaderProgram() {
    unsigned int vs = glCreateShader(GL_VERTEX_SHADER);
    glShaderSource(vs, 1, &vertexShaderSrc, nullptr);
    glCompileShader(vs);
    unsigned int fs = glCreateShader(GL_FRAGMENT_SHADER);
    glShaderSource(fs, 1, &fragmentShaderSrc, nullptr);
    glCompileShader(fs);
    unsigned int prog = glCreateProgram();
    glAttachShader(prog, vs);
    glAttachShader(prog, fs);
    glLinkProgram(prog);
    glDeleteShader(vs);
    glDeleteShader(fs);
    return prog;
}

// Configura los datos del triángulo en la GPU (usando píxeles)
void setupTriangle() {
    float vertices[] = {
        -40.0f, -40.0f, 0.0f, // Vértice inferior izquierdo (40 píxeles a la izquierda)
         40.0f, -40.0f, 0.0f, // Vértice inferior derecho (40 píxeles a la derecha)
         0.0f,   40.0f, 0.0f  // Vértice superior (40 píxeles arriba)
    };
    glGenVertexArrays(1, &VAO);
    glGenBuffers(1, &VBO);
    glBindVertexArray(VAO);
    glBindBuffer(GL_ARRAY_BUFFER, VBO);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
}

int main() {
    srand((unsigned int)time(NULL)); // Inicializar semilla aleatoria
    if (!glfwInit()) return -1;      // Iniciar GLFW

    // Configuración de versión OpenGL
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 4);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 6);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

    // Crear ventana
    GLFWwindow* mainWindow = glfwCreateWindow(SCR_WIDTH, SCR_HEIGHT, "DVD Rebound - Píxeles Reales", nullptr, nullptr);
    if (!mainWindow) { glfwTerminate(); return -1; }

    glfwMakeContextCurrent(mainWindow);                         // Activar contexto
    gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);          // Cargar funciones de OpenGL
    glfwSetFramebufferSizeCallback(mainWindow, framebuffer_size_callback); // Registrar callback
    glfwSwapInterval(1); // Sincronización vertical (V-Sync) para movimiento suave

    shaderProg = buildShaderProgram(); // Compilar Shaders
    setupTriangle();                   // Cargar Triángulo

    // Bucle principal
    while (!glfwWindowShouldClose(mainWindow)) {
        glfwPollEvents();   // Escuchar eventos
        processInput(mainWindow); // Procesar teclado

        // --- LÓGICA DE FÍSICA EN PÍXELES ---
        posX += velX;
        posY += velY;

        // Rebote Dinámico: Si la posición en píxeles sale de los límites de la ventana
        if (posX > (float)curWidth || posX < 0.0f) {
            velX *= -1.0f; // Invertir dirección horizontal
            setRandomRGB(); // Cambiar color
        }
        if (posY > (float)curHeight || posY < 0.0f) {
            velY *= -1.0f; // Invertir dirección vertical
            setRandomRGB(); // Cambiar color
        }

        // --- RENDERIZADO ---
        glClearColor(0.1f, 0.1f, 0.1f, 1.0f); // Fondo gris oscuro
        glClear(GL_COLOR_BUFFER_BIT);

        glUseProgram(shaderProg); // Usar programa de shaders

        // Enviar datos dinámicos a los Uniforms de los shaders
        glUniform2f(glGetUniformLocation(shaderProg, "screenRes"), (float)curWidth, (float)curHeight);
        glUniform2f(glGetUniformLocation(shaderProg, "pixelPos"), posX, posY);
        glUniform3f(glGetUniformLocation(shaderProg, "triangleColor"), r, g, b);

        glBindVertexArray(VAO);
        glDrawArrays(GL_TRIANGLES, 0, 3); // Dibujar triángulo

        glfwSwapBuffers(mainWindow); // Intercambiar buffers para mostrar imagen
    }

    // Limpieza final
    glDeleteVertexArrays(1, &VAO);
    glDeleteBuffers(1, &VBO);
    glDeleteProgram(shaderProg);
    glfwTerminate();
    return 0;
}
```

##### Evidencias

Evidencia 1 — Contexto y carga de OpenGL
Punto de inspección: Orden de las funciones glfwInit(), glfwMakeContextCurrent() y gladLoadGLLoader().

<img width="286" height="161" alt="image" src="https://github.com/user-attachments/assets/58c1a698-1957-4927-b846-ed19fd29df8a" />
<img width="738" height="311" alt="image" src="https://github.com/user-attachments/assets/0c3db1fa-9ddc-4d12-9410-f0abd247abd2" />

GLFW es la librería encargada de gestionar la ventana y el contexto de hardware. GLAD es un cargador de punteros a funciones; su trabajo es buscar las direcciones de las funciones de OpenGL en el driver de la tarjeta de video.
GLAD necesita saber qué contexto de OpenGL está activo para poder cargar las funciones correctas. Por esto es que GLFW debe inicializarse y crear una ventana primero. Sin un contexto actual, GLAD no tiene a donde mirar y fallará al cargar las funciones.


Evidencia 2 — Del arreglo al shader
Punto de inspección: Llamada a glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, ...) y su relación con layout(location = 0) in vec3 aPos.

<img width="836" height="255" alt="image" src="https://github.com/user-attachments/assets/a5d84986-a6b3-4dbc-81f3-993355d31d18" />
<img width="1161" height="84" alt="image" src="https://github.com/user-attachments/assets/9350dc5b-aec8-429e-81e9-7ec64648c612" />

El arreglo de vértices reside en la RAM (C++). Al usar glBufferData, lo enviamos a la GPU. Sin embargo, la GPU no sabe qué significan esos números. glVertexAttribPointer actúa como el "mapa" que le dice al shader: "A partir del location = 0, lee grupos de 3 floats".
El éxito de la evidencia es que el triángulo aparece en pantalla. Si el índice en C++ fuera 1 pero en el shader fuera location = 0, el pipeline no encontraría datos para aPos y no se dibujaría nada, confirmando que esta conexión es el puente vital entre los datos crudos y el procesamiento gráfico.

Evidencia 3 — Uniform y cambio visual
Punto de inspección: Uso de glUniform2f para mover el triángulo y glUniform3f para el color.
<img width="924" height="299" alt="image" src="https://github.com/user-attachments/assets/2e66b0c1-7f35-401e-91f1-8eec80356e43" />

<img width="1144" height="131" alt="image" src="https://github.com/user-attachments/assets/f7107eee-cef6-4225-9d19-b46fe4ea7487" />
<img width="1155" height="123" alt="image" src="https://github.com/user-attachments/assets/41250b33-e30c-40b2-8ca5-369ad8d02869" />

El VBO contiene la forma del triángulo. El uniform es una variable global en el shader que aplicamos después de leer los vértices pero antes de proyectarlos.
El pipeline de OpenGL permite aplicar transformaciones matemáticas en tiempo real. Esto es mucho más eficiente que modificar el VBO (que requeriría reenviar datos de la CPU a la GPU cada frame, saturando el bus de datos). El uniform cambia el estado del dibujo, no la definición del objeto.

Evidencia 4 — Prueba de borde (Test de Location)
Cambiar layout(location = 0) por layout(location = 5) en el Vertex Shader.
<img width="815" height="389" alt="image" src="https://github.com/user-attachments/assets/524ed338-c522-4531-a760-6e2fb4612290" />
<img width="1013" height="766" alt="image" src="https://github.com/user-attachments/assets/0436c0ba-2f40-4fb5-9273-376357b0499c" />

El triángulo desaparece por completo. La pantalla queda en negro. 
Las "locations" son direcciones estrictas. Si el código C++ envía datos al "puerto 0" pero el Shader los espera en el "puerto 5", la información se pierde en el pipeline. La comunicación entre CPU y GPU es por índices, no por nombres de variables.

Evidencia 5 — Responsabilidad del pipeline
Realizar el cálculo de la colisión y el rebote en C++ en lugar de hacerlo dentro del Vertex Shader?

Captura: (Captura del bloque if (posX > curWidth ...) en el main).

El cálculo de colisiones es una decisión física, no de representación visual. Si hiciéramos el rebote en el Shader, la CPU no sabría dónde está el triángulo. Al hacerlo en C++, podemos usar esa posición para otras cosas y enviamos el resultado final a la GPU mediante un uniform para que ella se encargue únicamente de pintar. Separación de responsabilidades: CPU para lógica, GPU para renderizado.

## Bitácora de reflexión
