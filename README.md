▓▓▓ C A L C U L A D O R A W E B (React + Vite) ▓▓▓
Suma · Resta · Multiplicación · División — con validaciones, pruebas, Docker y despliegue en la nube (AWS Amplify Hosting)
──────────────────────────────────────────────────────────────────────────────

[ Í N D I C E ]

DESCRIPCIÓN

OBJETIVOS DEL PROYECTO

REQUERIMIENTOS DEL CURSO (CHECKLIST)

TECNOLOGÍAS Y STACK

ESTRUCTURA DEL REPOSITORIO

EJECUCIÓN LOCAL (DESARROLLO)

PRUEBAS UNITARIAS

BUILD DE PRODUCCIÓN Y PREVISUALIZACIÓN

CONTAINER DOCKER (EVIDENCIA)

DESPLIEGUE EN LA NUBE (AWS AMPLIFY HOSTING)

BUENAS PRÁCTICAS DE GIT Y COLABORACIÓN

EVIDENCIAS (CAPTURAS)

APORTES INDIVIDUALES (TABLA)

SOLUCIÓN DE PROBLEMAS (FAQ)

NOTAS Y DECISIONES TÉCNICAS

LICENCIA

══════════════════════════════════════════════════════════════════════════════

D E S C R I P C I Ó N
══════════════════════════════════════════════════════════════════════════════
Aplicación web de CALCULADORA que realiza operaciones básicas:
• Suma, Resta, Multiplicación, División
• Validaciones: división por cero, inputs inválidos
• Pruebas unitarias (Vitest)
• Contenedor Docker
• Despliegue en la nube con AWS AMPLIFY HOSTING

NOTA IMPORTANTE:
Se intentó desplegar en AWS APP RUNNER (Docker desde ECR). 
Por tratarse de una cuenta nueva, el servicio requería verificación/opt-in y quedó en proceso. 
Para no bloquear la entrega, el despliegue final se realizó en AWS AMPLIFY HOSTING (estático). 
El proyecto incluye Docker como evidencia técnica local.

══════════════════════════════════════════════════════════════════════════════
2) O B J E T I V O S D E L P R O Y E C T O
══════════════════════════════════════════════════════════════════════════════
• Practicar flujo moderno: ramas, PRs, tests y despliegue.
• Asegurar calidad mínima mediante pruebas unitarias.
• Generar artefacto reproducible con Docker.
• Publicar en un entorno real (Amplify Hosting).

══════════════════════════════════════════════════════════════════════════════
3) R E Q U E R I M I E N T O S D E L C U R S O ( C H E C K L I S T )
══════════════════════════════════════════════════════════════════════════════
[ X ] App en React.js (Vite)
[ X ] Gestor de dependencias: npm
[ X ] Control de versiones: Git + GitHub (ramas y commits)
[ X ] Pruebas unitarias: Vitest
[ X ] Contenedor: Dockerfile funcional (build + run local)
[ X ] Despliegue: AWS Amplify Hosting (estático)
[ X ] Documentación: README + documento grupal con capturas

FUNCIONAL:
[ X ] Ingresar dos números
[ X ] Seleccionar operación (suma/resta/mul/div)
[ X ] Mostrar resultado
[ X ] Manejar errores (división por cero / inputs inválidos)

══════════════════════════════════════════════════════════════════════════════
4) T E C N O L O G Í A S Y S T A C K
══════════════════════════════════════════════════════════════════════════════
• Frontend: React 18 + Vite
• Estilos: CSS simple
• Pruebas: Vitest (+ Testing Library opcional)
• Empaquetado: Vite (build en /dist)
• Contenedor: Docker (multi-stage)
• Nube: AWS Amplify Hosting (SPA estática)

══════════════════════════════════════════════════════════════════════════════
5) E S T R U C T U R A D E L R E P O S I T O R I O
══════════════════════════════════════════════════════════════════════════════
.
├─ index.html
├─ package.json
├─ vite.config.js
├─ vitest.setup.js
├─ .gitignore
├─ .dockerignore
├─ Dockerfile
├─ src/
│ ├─ main.jsx
│ ├─ App.jsx
│ ├─ App.css
│ ├─ Calculadora.jsx
│ └─ lib/
│ └─ ops.js
└─ tests/
└─ calculadora.test.jsx

══════════════════════════════════════════════════════════════════════════════
6) E J E C U C I Ó N L O C A L ( D E S A R R O L L O )
══════════════════════════════════════════════════════════════════════════════
REQUISITOS: Node 18+

COMANDOS:
npm install
npm run dev
ABRIR: http://localhost:5173

══════════════════════════════════════════════════════════════════════════════
7) P R U E B A S U N I T A R I A S
══════════════════════════════════════════════════════════════════════════════
EJECUTAR (modo CLI):
npm run test -- --run

CON COBERTURA (opcional):
npm run test -- --coverage

Se prueban: sum, sub, mul, div y error en división por cero.

══════════════════════════════════════════════════════════════════════════════
8) B U I L D D E P R O D U C C I Ó N Y P R E V I S U A L I Z A C I Ó N
══════════════════════════════════════════════════════════════════════════════
npm run build → genera /dist
npm run preview → http://localhost:4173

══════════════════════════════════════════════════════════════════════════════
9) C O N T A I N E R D O C K E R
══════════════════════════════════════════════════════════════════════════════
Dockerfile (multi-stage, runtime con “serve” en puerto 3000):

FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci || npm install
COPY . .
RUN npm run build

FROM node:18-alpine AS runtime
WORKDIR /app
RUN npm install -g serve
COPY --from=build /app/dist ./dist
EXPOSE 3000
CMD ["serve", "-s", "dist", "-l", "3000"]

COMANDOS:
docker build -t calculadora-app:latest .
docker run --rm -p 3000:3000 calculadora-app:latest
ABRIR: http://localhost:3000


══════════════════════════════════════════════════════════════════════════════
10) D E S P L I E G U E E N L A N U B E ( A W S A M P L I F Y )
══════════════════════════════════════════════════════════════════════════════
MOTIVO: Cuenta nueva en AWS → App Runner requería activación/opt-in. Para cumplir la entrega, se usó AMPLIFY HOSTING (estático).

OPCIÓN A — Conectar GitHub (CI/CD):

Subir repo a GitHub (rama main).

AWS Console → Amplify → Hosting → New app → Host web app.

Conectar GitHub → seleccionar repo y rama main.

Build settings (amplify.yml para Vite):

version: 1
frontend:
phases:
preBuild:
commands:
- npm ci
build:
commands:
- npm run build
artifacts:
baseDirectory: dist
files:
- '/*'
cache:
paths:
- node_modules//*

Save & deploy → URL pública.

OPCIÓN B — Deploy manual (drag & drop):

npm run build → genera /dist

Amplify → Hosting → Deploy without Git

Subir el CONTENIDO de /dist (o un ZIP de /dist)

Amplify publica → URL pública

<img width="1384" height="758" alt="image" src="https://github.com/user-attachments/assets/d9308839-f08e-4e25-869d-9d26548132ee" />
<img width="1519" height="875" alt="image" src="https://github.com/user-attachments/assets/f681b799-f6f0-48f8-b3b5-a7664c80ae44" />
<img width="873" height="264" alt="image" src="https://github.com/user-attachments/assets/026262fa-9f0e-4c4d-b356-d3e158375380" />
<img width="1861" height="531" alt="image" src="https://github.com/user-attachments/assets/19e5e4ea-b37f-4125-b5f7-cbfc40817296" />
<img width="959" height="631" alt="image" src="https://github.com/user-attachments/assets/c006c501-6fa5-448c-9bbd-744ec1dad03d" />


══════════════════════════════════════════════════════════════════════════════
11) B U E N A S P R Á C T I C A S D E G I T Y C O L A B O R A C I Ó N
══════════════════════════════════════════════════════════════════════════════
RAMAS RECOMENDADAS:
• main → producción (despliegue)
• develop → integración diaria
• feature/<iniciales>-<tarea> → trabajo por feature

PRÁCTICAS:
• Pull Requests a develop; luego release a main
• Commits estilo “Conventional Commits” (feat:/fix:/test:/docs:)
• Issues/Projects para visibilidad

══════════════════════════════════════════════════════════════════════════════
12) E V I D E N C I A S ( C A P T U R A S )
══════════════════════════════════════════════════════════════════════════════
CREAR CARPETA: /docs/capturas y adjuntar imágenes

(1) GitHub
– Ramas (main, develop, feature/*), Pull Requests, commits
– docs/capturas/github-ramas.png
– docs/capturas/github-prs.png

(2) Pruebas
– Salida de “npm run test -- --run” en verde
– docs/capturas/tests-ok.png

(3) Docker
– docker build / docker run / navegador en http://localhost:3000

– docs/capturas/docker-build.png
– docs/capturas/docker-run.png

(4) Despliegue en la nube (Amplify)
– Build en verde
– URL pública con la app abierta
– docs/capturas/amplify-build.png
– docs/capturas/amplify-url.png



══════════════════════════════════════════════════════════════════════════════
13) L I C E N C I A
══════════════════════════════════════════════════════════════════════════════
Proyecto para uso académico. Puede reutilizarse citando la fuente del equipo.
