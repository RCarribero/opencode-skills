---
name: mega-test-library
description: Biblioteca de +100 casos de prueba categorizados para testing automatizado. Usar cuando el Test Coordinator necesite cobertura exhaustiva.
---

# Mega Test Library (+100 Escenarios)

Usa esta lista como checklist. El Test Coordinator debe seleccionar los relevantes segun el stack y contexto del proyecto, y expandirlos a codigo real.

## UNIT TESTS (1-30)
1. Funcion pura con entrada valida → output esperado
2. Funcion pura con entrada invalida → error manejado
3. Funcion con argumentos nulos/undefined
4. Funcion con strings vacios
5. Funcion con numeros maximos/minimos (boundary)
6. Funcion con decimales y precision
7. Funcion con arrays vacios
8. Funcion con arrays de 1 elemento
9. Funcion con arrays grandes (10k+ items)
10. Funcion con objetos anidados
11. Funcion con referencias circulares
12. Funcion con fechas validas
13. Funcion con fechas invalidas o pasadas
14. Funcion con timezones diferentes
15. Funcion con caracteres especiales/unicode/emojis
16. Funcion con inputs muy largos (buffer overflow check)
17. Funcion con regex malicioso (ReDoS)
18. Funcion con concurrencia basica (race condition check)
19. Funcion con cache hit/miss
20. Funcion con memoizacion activa
21. Funcion con side-effects mockeados
22. Funcion con callbacks exitosos
23. Funcion con callbacks fallidos
24. Funcion con promesas resueltas
25. Funcion con promesas rechazadas
26. Funcion con async/await timeout
27. Funcion con generadores (yield)
28. Funcion con clases y herencia
29. Funcion con interfaces/types
30. Funcion con enums y valores invalidos

## INTEGRATION TESTS (31-50)
31. API REST GET 200 con payload valido
32. API REST GET 404 recurso inexistente
33. API REST POST 201 creado exitosamente
34. API REST POST 400 validacion fallida
35. API REST PUT 200 actualizacion parcial
36. API REST DELETE 204 eliminado
37. API REST con autenticacion JWT valida
38. API REST con token expirado
39. API REST con token invalido
40. API REST con rate limiting activo
41. API REST con CORS bloqueado
42. API REST con payload malformado (no JSON)
43. API REST con query params complejos
44. API REST con pagination (limit/offset)
45. API REST con filtros combinados
46. API REST con ordenamiento multiple
47. DB connection exitosa
48. DB query con transaccion rollback
49. DB query con transaccion commit
50. DB query con deadlock simulado

## E2E / FLUJO TESTS (51-65)
51. Login exitoso → redireccion a dashboard
52. Login fallido → mensaje de error
53. Registro de usuario nuevo → email de verificacion
54. Recuperacion de contraseña → link valido
55. Flujo de checkout completo (carrito → pago → confirmacion)
56. Flujo de checkout con pago fallido
57. Flujo de checkout con stock insuficiente
58. Upload de archivo valido → preview y guardado
59. Upload de archivo invalido (tipo/tamano)
60. Busqueda con resultados → paginacion
61. Busqueda sin resultados → estado vacio
62. Navegacion entre rutas protegidas
63. Navegacion entre rutas publicas
64. Cierre de sesion → limpieza de tokens
65. Actualizacion de perfil → persistencia

## SECURITY TESTS (66-80)
66. SQL injection en input de texto
67. XSS reflejado en output
68. XSS almacenado en base de datos
69. CSRF en formulario POST
70. Path traversal en upload/download
71. Command injection en parametros de sistema
72. Header spoofing (X-Forwarded-For)
73. JWT tampering (firma invalida)
74. JWT algorithm confusion (none)
75. Rate limiting bypass (IP rotation)
76. Privilege escalation (usuario → admin)
77. IDOR en recursos de otros usuarios
78. Directory listing expuesto
79. Versiones de librerias con CVEs conocidos
80. Logs con datos sensibles (PII, tokens)

## PERFORMANCE TESTS (81-90)
81. Tiempo de respuesta < 200ms (p95)
82. Tiempo de respuesta < 500ms (p99)
83. Throughput > 1000 req/s
84. Uso de memoria estable bajo carga
85. Uso de CPU < 80% bajo carga
86. Connection pooling eficiente
87. Cache hit ratio > 80%
88. DB query execution < 50ms
89. Asset loading < 1s (LCP)
90. Time to Interactive < 3s

## EDGE CASES / BOUNDARY (91-105)
91. Input con 0 caracteres
92. Input con 1 caracter
93. Input con longitud maxima permitida
94. Input con longitud maxima + 1
95. Array con 0 elementos
96. Array con 1 elemento
97. Array con elementos duplicados
98. Array con elementos null/undefined
99. Fecha: 29 de febrero (año bisiesto)
100. Fecha: 1 de enero / 31 de diciembre
101. Hora: cambio de horario (DST)
102. Moneda: decimales > 2
103. Moneda: valores negativos
104. Moneda: 0.00
105. Concurrent requests al mismo recurso

## COMO USAR
1. El `@test-coordinator` selecciona los IDs relevantes segun el contexto.
2. Expande cada ID a un test real con el framework del proyecto.
3. Agrupa tests por categoria en archivos separados.
4. Ejecuta y reporta cobertura.
