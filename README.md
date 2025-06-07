# Uso-de-Automatas-Celulares-en-los-incendios-forestales

import numpy as np
import matplotlib.pyplot as plt
import matplotlib.animation as animation

# Estados de las celdas
VEGETACION = 0  # Verde
FUEGO = 1       # Rojo
QUEMADO = 2     # Negro

# Parámetros configurables
TAMANO = 50              # Tamaño de la grilla (50x50)
PROB_PROGAGACION = 0.6   # Probabilidad de que el fuego se propague a una celda vecina

# Inicialización de la grilla
def iniciar_grilla():
    grilla = np.zeros((TAMANO, TAMANO), dtype=int)
    # Encender un fuego en el centro
    centro = TAMANO // 2
    grilla[centro, centro] = FUEGO
    return grilla

# Actualizar el estado de la grilla
def actualizar(grilla):
    nueva = grilla.copy()
    for i in range(TAMANO):
        for j in range(TAMANO):
            if grilla[i, j] == VEGETACION:
                # Ver si alguna celda vecina está en fuego
                vecinos = grilla[max(0, i-1):min(TAMANO, i+2), max(0, j-1):min(TAMANO, j+2)]
                if FUEGO in vecinos:
                    if np.random.rand() < PROB_PROGAGACION:
                        nueva[i, j] = FUEGO
            elif grilla[i, j] == FUEGO:
                nueva[i, j] = QUEMADO
    return nueva

# Colores para visualizar
colores = {
    VEGETACION: (0.0, 0.6, 0.0),  # Verde
    FUEGO: (1.0, 0.0, 0.0),       # Rojo
    QUEMADO: (0.1, 0.1, 0.1)      # Gris oscuro
}

def dibujar(grilla):
    img = np.zeros((TAMANO, TAMANO, 3))
    for estado, color in colores.items():
        img[grilla == estado] = color
    return img

# Configurar la animación
grilla = iniciar_grilla()

fig, ax = plt.subplots()
im = ax.imshow(dibujar(grilla), interpolation='nearest')
ax.set_title("Simulación de incendio forestal")
ax.axis('off')

def animar(frame):
    global grilla
    grilla = actualizar(grilla)
    im.set_data(dibujar(grilla))
    return [im]

ani = animation.FuncAnimation(fig, animar, frames=100, interval=200, blit=True)
plt.show()
