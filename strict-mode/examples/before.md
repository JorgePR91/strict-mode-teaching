# Ejemplo — SIN strict mode

Conversación típica con un asistente de IA configurado por defecto.
El usuario está **aprendiendo** Node.js y Express.

---

**Usuario:**
Quiero crear un endpoint de login en Express que valide email y contraseña contra una base de datos.

---

**Asistente (sin strict mode):**

Aquí tienes la implementación completa:

```js
// server.js
const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const { PrismaClient } = require('@prisma/client');

const app = express();
const prisma = new PrismaClient();
app.use(express.json());

const JWT_SECRET = process.env.JWT_SECRET || 'changeme';

app.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    if (!email || !password) {
      return res.status(400).json({ error: 'Missing credentials' });
    }
    const user = await prisma.user.findUnique({ where: { email } });
    if (!user) return res.status(401).json({ error: 'Invalid credentials' });
    const valid = await bcrypt.compare(password, user.passwordHash);
    if (!valid) return res.status(401).json({ error: 'Invalid credentials' });
    const token = jwt.sign({ userId: user.id }, JWT_SECRET, { expiresIn: '1h' });
    return res.json({ token });
  } catch (err) {
    console.error(err);
    return res.status(500).json({ error: 'Internal server error' });
  }
});

app.listen(3000, () => console.log('Listening on 3000'));
```

Instálalo con:

```
npm install express bcrypt jsonwebtoken @prisma/client
```

Listo. Si quieres, te añado también refresh tokens, rate limiting y middleware de autenticación.

---

## Qué ha ido mal aquí

- El usuario no sabe qué es **bcrypt**, **jwt**, ni **Prisma** — pero ya están en su código.
- No sabe qué es un **JWT secret** ni por qué hay un fallback a `'changeme'`.
- No entiende por qué algunas respuestas devuelven 400 y otras 401.
- No sabe qué es `bcrypt.compare` ni por qué no se compara con `===`.
- Recibe el ofrecimiento de añadir más cosas (refresh tokens, rate limiting) que tampoco entiende.

**Resultado:** el usuario tiene código que funciona, pero **no ha aprendido nada**. La siguiente vez que algo falle, no sabrá ni por dónde mirar.
