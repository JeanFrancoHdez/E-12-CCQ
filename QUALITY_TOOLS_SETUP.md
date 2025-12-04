# Configuración de Herramientas de Calidad - QuickPark

Este documento explica la configuración de SonarCloud y Coveralls para el proyecto QuickPark.

## 🔧 Configuración Completada

### 1. SonarCloud (`sonar-project.properties`)

El archivo `sonar-project.properties` está configurado con:

#### **5 Reglas de Calidad Principales:**

1. **Cobertura de Código Mínima**: 70%
   - Asegura que al menos el 70% del código está cubierto por tests

2. **Duplicación de Código Máxima**: 3%
   - Evita código duplicado en el proyecto (excluye tests)

3. **Complejidad Ciclomática**: Máximo 15 por función
   - Limita la complejidad de las funciones para mantener código mantenible

4. **Codificación**: UTF-8
   - Garantiza una codificación consistente

5. **Issues Críticos y Bloqueantes**: 0
   - No se permiten issues críticos o bloqueantes (se configura en el Quality Gate de SonarCloud)

#### **Configuración de Análisis:**
- **Sources**: `backend/src`, `frontend/src`
- **Tests**: `backend/src/tests`, `e2e-tests`
- **Exclusiones**: node_modules, dist, coverage, archivos de test y configuración
- **Reportes de Cobertura**: `backend/coverage/lcov.info`

### 2. GitHub Actions (`.github/workflows/quality-analysis.yml`)

El workflow ya está configurado para:
- Ejecutar tests con cobertura
- Enviar resultados a SonarCloud
- Enviar resultados a Coveralls
- Se ejecuta en push y pull requests a `main`

## 📋 Pasos para Completar la Configuración

### Paso 1: Configurar Secretos en GitHub

Ve a tu repositorio en GitHub: `Settings` → `Secrets and variables` → `Actions`

#### Secretos Requeridos:

1. **`SONAR_TOKEN`**
   - Ve a [SonarCloud](https://sonarcloud.io)
   - Ve a tu proyecto → `Administration` → `Analysis Method`
   - Copia el token generado
   - Agrégalo como secret en GitHub

2. **`GITHUB_TOKEN`**
   - Este se crea automáticamente por GitHub Actions
   - No necesitas hacer nada

3. **Para Coveralls** (opcional si quieres usar Coveralls):
   - Ve a [Coveralls.io](https://coveralls.io)
   - Conecta tu repositorio
   - El token se puede configurar si es necesario

### Paso 2: Configurar el Quality Gate en SonarCloud

1. Ve a [SonarCloud](https://sonarcloud.io)
2. Selecciona tu proyecto `E-12-CCQ`
3. Ve a `Quality Gates` → `Project Settings`
4. Configura el Quality Gate con estas condiciones:
   - Coverage: >= 70%
   - Duplicated Lines: <= 3%
   - Blocker Issues: 0
   - Critical Issues: 0
   - Maintainability Rating: A

### Paso 3: Arreglar Tests con Stripe

Los tests están fallando porque falta la configuración de Stripe. Agrega al archivo `.env` en `backend/`:

```env
STRIPE_SECRET_KEY=sk_test_tu_clave_de_prueba_aqui
```

O configura los tests para usar mocks de Stripe.

### Paso 4: Verificar Localmente

```bash
# Instalar dependencias
npm install
cd backend && npm install

# Ejecutar tests con cobertura
npm run test:coverage

# Verificar que se generó el reporte
ls -la backend/coverage/lcov.info
```

### Paso 5: Push a GitHub

Una vez que los tests pasen localmente:

```bash
git add .
git commit -m "feat: configuración de SonarCloud y Coveralls"
git push origin main
```

## 🎯 Verificación del Workflow

Después del push, verifica:

1. **GitHub Actions**:
   - Ve a la pestaña `Actions` en tu repositorio
   - Verifica que el workflow `Quality Analysis` se ejecute correctamente

2. **SonarCloud**:
   - Ve a [SonarCloud](https://sonarcloud.io)
   - Verifica que el análisis se completó
   - Revisa el Quality Gate status

3. **Coveralls**:
   - Ve a [Coveralls.io](https://coveralls.io)
   - Verifica que los reportes de cobertura se subieron correctamente

## 🚀 Comandos Útiles

```bash
# Ejecutar tests
npm test

# Ejecutar tests con cobertura
npm run test:coverage

# Ejecutar solo tests del backend
cd backend && npm test

# Ver resultados de cobertura (después de ejecutar tests)
open backend/coverage/index.html  # En macOS
xdg-open backend/coverage/index.html  # En Linux
```

## 📊 Badges (Opcional)

Puedes agregar badges al README.md principal:

```markdown
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=JeanFrancoHdez_E-12-CCQ&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=JeanFrancoHdez_E-12-CCQ)
[![Coverage Status](https://coveralls.io/repos/github/JeanFrancoHdez/E-12-CCQ/badge.svg?branch=main)](https://coveralls.io/github/JeanFrancoHdez/E-12-CCQ?branch=main)
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=JeanFrancoHdez_E-12-CCQ&metric=sqale_rating)](https://sonarcloud.io/summary/new_code?id=JeanFrancoHdez_E-12-CCQ)
```

## 🔍 Solución de Problemas

### Los tests fallan con error de Stripe
- Asegúrate de tener `STRIPE_SECRET_KEY` en tu archivo `.env`
- O configura mocks para los tests

### SonarCloud no recibe datos
- Verifica que el token `SONAR_TOKEN` esté configurado en GitHub Secrets
- Asegúrate de que el proyecto existe en SonarCloud
- Revisa los logs del workflow en GitHub Actions

### Coveralls no funciona
- Verifica que tu repositorio esté conectado en Coveralls.io
- Asegúrate de que el archivo `lcov.info` se está generando correctamente

## 📝 Notas

- El análisis se ejecuta automáticamente en cada push y pull request
- Los reportes de cobertura solo se generan cuando los tests pasan
- SonarCloud analiza tanto el código de backend como de frontend
- Las reglas de calidad están configuradas para mantener un código limpio y mantenible
