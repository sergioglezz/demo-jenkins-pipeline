# Calculadora Flask con pipeline de Jenkins

Una API de calculadora muy sencilla en **Python + Flask**, que sirve como base para montar un **pipeline de Jenkins** con todos los tipos de prueba habituales: unitarias, de servicio, estáticas, de seguridad, de cobertura y de rendimiento.

*Proyecto de la asignatura DevOps & Cloud de UNIR.*

## La API

| Ruta | Ejemplo | Respuesta |
|---|---|---|
| `GET /` | `/` | `Hello from The Calculator!` |
| `GET /calc/add/<a>/<b>` | `/calc/add/1/2` | `3` |
| `GET /calc/substract/<a>/<b>` | `/calc/substract/5/3` | `2` |

Si algún operando no es un número, devuelve `400`.

## El pipeline

| Etapa | Herramienta | Qué hace |
|---|---|---|
| Unit | pytest | Prueba la lógica de la calculadora |
| Services | pytest + WireMock | Prueba la API arrancada y un servicio simulado con WireMock |
| Static | flake8 | Revisa el estilo del código |
| Security | bandit | Busca problemas de seguridad |
| Cobertura | coverage | Exige un mínimo del 85 % de líneas y el 80 % de ramas |
| Performance | JMeter | Carga de 5 usuarios × 8 peticiones sobre la API |

Hay dos versiones:

- **`JENKINSFILE`**: todo en un mismo agente.
- **`JENKINSFILE_agentes`**: reparte el trabajo entre tres agentes y ejecuta en paralelo las pruebas de calidad y las de servicio.

> Los pipelines están pensados para agentes **Windows** (usan `bat`), y la ruta de JMeter es la de mi equipo.

## Probarlo en local

```bash
pip install flask pytest

# Tests unitarios
pytest test/unit

# Arrancar la API en http://localhost:5000
flask --app app/api.py run
```

Los tests de `test/rest` necesitan la API arrancada y WireMock en el puerto 9090:

```bash
java -jar test/wiremock/wiremock-standalone-3.13.2.jar --port 9090 --root-dir test/wiremock
pytest test/rest
```

## Estructura

```
app/        Código de la API (Flask) y de la calculadora
test/
├── unit/       Tests unitarios
├── rest/       Tests de la API
├── wiremock/   Servicio simulado
└── jmeter/     Prueba de rendimiento
```
