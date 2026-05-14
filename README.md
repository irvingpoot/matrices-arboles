# Proyecto final — Fase 2

Aplicación web que calcula y visualiza la **matriz de adyacencia** y la **matriz de incidencia** de un grafo no dirigido a partir de los nodos y aristas ingresados por el usuario.

---

## Tecnologías

- [Astro 6:](https://astro.build/) framework de generación de sitios web
- [Tailwind CSS 4:](https://tailwindcss.com/) estilos utilitarios
- [Fraunces:](https://fonts.google.com/specimen/Fraunces) tipografía principal (vía `@fontsource-variable`)
- TypeScript: tipado estático en el script del cliente

---

## Estructura del proyecto

```
src/
├── components/
│   └── Edge.astro          # Componente de una arista (par de inputs)
├── layouts/
│   └── Layout.astro        # Layout base HTML con tipografía y estilos globales
├── pages/
│   └── index.astro         # Página principal: formulario, lógica y matrices
└── styles/
    └── global.css          # Importación de Tailwind y fuentes
```

---

## Cómo ejecutar

```bash
# Instalar dependencias
npm install

# Servidor de desarrollo
npm run dev

# Build de producción
npm run build
```

---

## Cómo usar la aplicación

1. **Ingresa los nodos:** escribe una letra por cada campo (A–Z). Se aceptan solamente 8 nodos. No se permiten números, símbolos ni letras repetidas.
2. **Ingresa las aristas:** cada arista es un par `(nodo origen, nodo destino)`. Hay que definir 12 aristas.
3. **Presiona "Generar matrices":** la aplicación calcula y muestra ambas matrices.
4. **Presiona "Limpiar campos":** resetea todos los inputs y las matrices.

---

## Teoría vista en clase

### Matriz de Adyacencia

Matriz cuadrada **n × n** (donde n = número de nodos). El valor `A[i][j] = 1` indica que existe una arista entre el nodo `i` y el nodo `j`. Como el grafo es **no dirigido**, la matriz es simétrica: `A[i][j] = A[j][i]`.

Ejemplo para los nodos `{A, B, C}` con aristas `(A,B)` y `(B,C)`:

```
   A  B  C
A  0  1  0
B  1  0  1
C  0  1  0
```

### Matriz de Incidencia

Matriz **n × m** (n = nodos, m = aristas). El valor `B[i][e] = 1` indica que el nodo `i` es un extremo de la arista `e`. Cada columna tiene exactamente dos unos: los dos nodos que conecta esa arista.

Ejemplo para los mismos nodos y aristas (`e1 = (A,B)`, `e2 = (B,C)`):

```
   e1  e2
A   1   0
B   1   1
C   0   1
```

---

## Validaciones

| Caso | Comportamiento |
|---|---|
| Caracter no letra (número, símbolo) | Se elimina al instante mientras se escribe |
| Texto pegado con caracteres inválidos | Se extrae solo la primera letra válida |
| Menos de 2 nodos | Modal de error |
| Nodos duplicados | Modal de error |
| Arista con solo un extremo definido | Modal de error |
| Nodo en arista que no existe en la lista | Modal de error |
| Sin aristas definidas | Modal de error |

El modal se cierra con el botón **Entendido**, haciendo clic fuera de él, o presionando `Escape`.

---

## Arquitectura del script

El script del cliente (`index.astro`) está organizado en módulos con responsabilidad única siguiendo principios SOLID:

| Módulo | Funciones | Responsabilidad |
|---|---|---|
| Modal | `showModal`, `closeModal` | Mostrar y ocultar errores |
| Validación en tiempo real | `attachLiveValidation` | Sanitizar inputs mientras se escribe |
| Lectura de datos | `readNodes`, `readEdges` | Leer y validar el formulario |
| Cálculo | `buildAdjacencyMatrix`, `buildIncidenceMatrix` | Calcular las matrices (funciones puras) |
| Renderizado | `renderMatrix` | Convertir matrices a HTML |
| Función principal | `handleGenerarClick` | Orquestar el flujo completo |

Las funciones de cálculo son **puras**: dado el mismo input siempre producen el mismo output, sin efectos secundarios.

Las funciones de lectura retornan un objeto resultado `{ ok: true, data }` o `{ ok: false, message }` en lugar de lanzar excepciones, lo que hace el flujo de errores explícito y predecible.

---