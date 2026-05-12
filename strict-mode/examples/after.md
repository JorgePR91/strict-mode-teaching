# Ejemplo — CON strict mode

Misma petición, mismo usuario, mismo asistente — pero con las
instrucciones de `strict-mode.md` cargadas en el contexto.

---

**Usuario:**
Quiero crear un endpoint de login en Express que valide email y contraseña contra una base de datos.

---

**Asistente (con strict mode):**

He clasificado tu petición como **Categoría A — Implementación**.
Antes de tocar código, vamos a dividir el problema. Un login completo incluye
varias responsabilidades distintas y mezclarlas en un solo paso te impediría
entender por qué cada parte existe.

## Pasos que propongo

1. Crear el endpoint `POST /login` vacío que solo lea `email` y `password` del body y devuelva un 200 de prueba.
2. Validar que ambos campos llegan y no están vacíos (responder 400 si no).
3. Buscar el usuario en base de datos por email.
4. Comparar la contraseña recibida con el hash almacenado.
5. Generar y devolver un token de sesión (JWT).

Cinco pasos pequeños, cada uno verificable por separado.

## Antes de empezar, dos preguntas

- ¿Ya tienes un servidor Express corriendo, o partimos desde cero?
- ¿Qué usas para acceder a la base de datos (Prisma, Sequelize, query SQL directa…)?

En cuanto me confirmes, **pasamos al paso 1** y solo al paso 1.

---

## Qué ha cambiado aquí

- El asistente **no ha generado código todavía**.
- Ha hecho visible la estructura del problema antes de implementar.
- Ha preguntado por el contexto del usuario antes de asumir tecnologías.
- Ha dejado claro que cada paso será explicado y verificado por separado.

**Resultado:** el usuario va a tardar más en tener el endpoint funcionando,
pero al terminar va a **entender qué hay en cada línea** y por qué existe.
La próxima vez que algo falle, sabrá dónde mirar.
