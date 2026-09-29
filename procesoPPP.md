# Proceso de Prácticas Pre Profesionales (PPP) — Plan 2021

> Programa Académico de Ingeniería de Sistemas e Informática — Facultad de Ingeniería, UDH
> Documento de referencia para el equipo del proyecto de Servicio Social.
>
> **Fuentes:**
> - Reglamento de PPP del P.A. de Ing. de Sistemas e Informática — 2021 (Modalidad Presencial)
>   · Res. N° 221-2021-CF-FI-UDH · ratificado por Res. N° 029-2022-R-CU-UDH (01/02/2022)
> - Flujograma operativo entregado por Coordinación
>
> **Regla de precedencia aplicada:** el reglamento define los plazos y condiciones de fondo;
> el flujograma define la operativa vigente (secuencia de trámites, correos, formatos).
> Donde hubo discrepancia, se optó por lo indicado por Coordinación y queda anotado en la sección 11.

---

## 1. Actores

| Código | Actor | Rol |
|---|---|---|
| `EST` | Estudiante | Inicia todos los trámites |
| `EMP` | Entidad receptora (empresa/institución) | Acepta al practicante, evalúa y certifica |
| `PA` | P.A. de Ingeniería de Sistemas | Recepciona, verifica y deriva |
| `COM` | Comisión de PPP | Revisa, aprueba, designa supervisor, evalúa y sustenta |
| `FAC` | Facultad de Ingeniería (Decanato) | Emite carta de presentación y resolución final |
| `SUP` | Asesor–Supervisor (docente) | Acompaña la ejecución y evalúa |

## 2. Condiciones para iniciar la PPP

| Condición | Detalle | Base |
|---|---|---|
| Ciclo | Haber concluido satisfactoriamente todos los cursos de especialidad **hasta el octavo ciclo** | Art. 15 |
| Duración | **600 horas** efectivas | Art. 15 |
| Horario | No debe superponerse con el horario de clases | Art. 15 |
| Obligatoriedad | Requisito indispensable para el Grado de Bachiller. Sin exoneración | Arts. 1 y 3 |
| Dónde | Empresa o institución pública o privada del rubro, que cuente con un Ing. de Sistemas o carrera afín para supervisar | Arts. 4 y 6 |
| Áreas válidas | Desarrollo de sistemas · Infraestructura, redes y comunicaciones · Seguridad y auditoría · Gestión de TIC · Gestión de proyectos | Art. 14 |

## 3. Correos y pagos

| Etapa | Correo destino | Pago |
|---|---|---|
| Carta de presentación | `secretaria.fac.ing@udh.edu.pe` | S/ 5.00 |
| Plan de PPP | `comision.practicas.isi@udh.edu.pe` | — |
| Informe final de PPP | `secretaria.sistemas.hco@udh.edu.pe` | — |

⚠️ **El correo cambia en cada etapa.** Es el error más común y el chatbot debe resolverlo con precisión.

## 4. Plazos

| Hito | Plazo | Base |
|---|---|---|
| Presentar el Plan de PPP | **15 días calendarios** luego de iniciada la práctica | Art. 18 |
| Subsanar observaciones del Plan | **7 días hábiles** desde la entrega de observaciones | Art. 22 |
| Presentar el Informe final | **20 días hábiles** después de concluida la práctica | Art. 28 |
| Subsanar observaciones tras la sustentación | **10 días hábiles** · su incumplimiento anula la práctica | Art. 30 |
| Reinicio tras anulación | **30 días calendarios**, en otra empresa o institución | Arts. 21 y 22 |

## 5. Anexos del reglamento

| Anexo | Documento | Etapa |
|---|---|---|
| Anexo 02 | Carta de compromiso | Carta de presentación |
| Anexo 03 | Carta de aceptación de la entidad receptora | Carta de presentación |
| Anexo 04 | Esquema del Plan de Trabajo | Plan de PPP |
| Anexo 05 | Estructura del Informe Final | Informe final |
| Anexo 06 | Ficha de evaluación (jefe de área) | Informe final |
| Anexo 07 | Ficha de supervisión (asesor–supervisor) | Informe final |
| Anexo 10 | Ficha de avances | Durante la ejecución |

> Los Anexos 08 y 09 pertenecen al proceso de **convalidación** de PPP (ver sección 10).

---

## 6. Vista general del proceso

```mermaid
flowchart TD
    A([Estudiante apto<br/>8vo ciclo concluido]) --> B[ETAPA 1<br/>Carta de presentación]
    B --> C[ETAPA 2<br/>Plan de PPP]
    C --> D[ETAPA 3<br/>Ejecución de la práctica<br/>600 horas]
    D --> E[ETAPA 4<br/>Informe final y sustentación]
    E --> F([Resolución de aprobación<br/>Facultad de Ingeniería])

    B -.-> B1["secretaria.fac.ing<br/>Pago S/ 5.00 · Anexos 02 y 03"]
    C -.-> C1["comision.practicas.isi<br/>15 días calendarios · Anexo 04"]
    D -.-> D1["Asesor–Supervisor<br/>Ficha de avances (Anexo 10)"]
    E -.-> E1["secretaria.sistemas.hco<br/>20 días hábiles · Todo en PDF"]

    style B fill:#d5e8d4,stroke:#333
    style C fill:#f8cecc,stroke:#333
    style D fill:#fff2cc,stroke:#333
    style E fill:#e1d5e7,stroke:#333
    style F fill:#d4e1f5,stroke:#333
```

---

## 7. Etapa 1 — Carta de presentación

```mermaid
sequenceDiagram
    autonumber
    actor EST as Estudiante
    participant EMP as Entidad receptora
    participant PA as P.A. Ing. Sistemas
    participant FAC as Facultad de Ingeniería

    EST->>EMP: Gestiona su aceptación como practicante
    EMP-->>EST: CARTA DE ACEPTACIÓN (ANEXO 03)<br/>dirigida al Decano: fecha de inicio, área,<br/>jefe inmediato, funciones y horario
    EST->>EST: Elabora CARTA DE COMPROMISO (ANEXO 02)

    EST->>EST: Solicita el trámite en el sistema UDH
    EST->>EST: Realiza el pago de S/ 5.00
    EST->>FAC: Envía a secretaria.fac.ing@udh.edu.pe<br/>Anexo 02 + Anexo 03

    PA->>PA: Verifica aprobación de Of. Matrícula
    PA->>PA: Verifica aprobación de Of. Tesorería
    PA->>FAC: Da conformidad a los datos y pasa el expediente

    FAC->>FAC: Recepciona carta de aceptación y de compromiso
    FAC-->>EST: Emite la CARTA DE PRESENTACIÓN<br/>al correo institucional del estudiante
```

**Salida:** carta de presentación firmada por el Decanato, en el correo institucional del estudiante.

---

## 8. Etapa 2 — Plan de PPP

```mermaid
sequenceDiagram
    autonumber
    actor EST as Estudiante
    participant EMP as Entidad receptora
    participant PA as P.A. Ing. Sistemas
    participant COM as Comisión de PPP
    participant SUP as Asesor–Supervisor

    Note over EST: Plazo: 15 días calendarios<br/>desde iniciada la práctica (Art. 18)

    EST->>EMP: Presenta la carta de presentación
    EST->>EST: Elabora el PLAN DE PPP según ANEXO 04

    EST->>EST: Solicita APROBACIÓN DE PLAN DE PPP en el sistema
    EST->>COM: Envía a comision.practicas.isi@udh.edu.pe:<br/>Plan de PPP · Carta de presentación<br/>Carta de compromiso · Carta de aceptación

    PA->>PA: Recepciona y verifica los requisitos
    PA->>COM: Deriva el expediente a la Comisión

    COM->>COM: Revisa, analiza y observa o aprueba el Plan

    alt Plan observado
        COM-->>EST: Entrega observaciones
        Note over EST: 7 días hábiles para subsanar (Art. 22)
        EST->>COM: Subsana y reenvía
    else Plazo excedido
        COM-->>EST: ANULA la práctica<br/>Reinicio en 30 días calendarios, en otra empresa
    end

    COM->>SUP: Designa al Asesor–Supervisor (Art. 23)
    COM-->>EST: Aprueba y autoriza el Plan de PPP

    Note over EST,SUP: Indicación de Coordinación:<br/>no volver a llamar hasta culminar la PPP.
```

**Salida:** Plan de PPP aprobado por la Comisión y Asesor–Supervisor designado. No requiere resolución de Facultad.

---

## 9. Etapa 3 — Ejecución de la práctica

```mermaid
sequenceDiagram
    autonumber
    actor EST as Estudiante
    participant EMP as Entidad receptora
    participant SUP as Asesor–Supervisor
    participant COM as Comisión de PPP

    Note over EST,EMP: 600 horas efectivas (Art. 15)

    loop Mensualmente o mínimo 2 reuniones
        SUP->>EST: Reunión de seguimiento del Plan
        SUP->>COM: Eleva la FICHA DE AVANCES (ANEXO 10)
    end

    EST->>EST: Registra asistencia firmada y sellada por la empresa

    Note over SUP,EMP: Próximo a culminar la práctica
    SUP->>EMP: Visita la institución para evaluar
    SUP->>COM: Eleva la FICHA DE SUPERVISIÓN (ANEXO 07)
```

### Causales de anulación o desaprobación (Art. 40)

- Incumplir lo establecido en el reglamento
- Presentar documento falsificado o adulterado
- Presentar un informe plagiado
- Faltar sin justificación 3 días consecutivos o 5 alternados
- No encontrarse en el centro de prácticas durante la supervisión
- Informe de desaprobación del tutor de la empresa o del asesor–supervisor

---

## 10. Etapa 4 — Informe final y sustentación

```mermaid
sequenceDiagram
    autonumber
    actor EST as Estudiante
    participant EMP as Entidad receptora
    participant PA as P.A. Ing. Sistemas
    participant COM as Comisión de PPP
    participant FAC as Facultad de Ingeniería

    Note over EST: Plazo: 20 días hábiles<br/>después de concluida la práctica

    EST->>EMP: Solicita documentos de cierre
    EMP-->>EST: Constancia de PPP (fecha de inicio, término y labores)
    EMP-->>EST: Ficha de evaluación del jefe de área (ANEXO 06)
    EMP-->>EST: Formato de asistencia firmado y sellado
    EST->>EST: Elabora el Informe de PPP según ANEXO 05
    EST->>EST: Recaba la ficha de supervisión (ANEXO 07)
    EST->>EST: Se inscribe en Seguimiento del Graduado

    EST->>EST: Solicita APROBACIÓN DEL INFORME FINAL DE PPP
    EST->>PA: Envía TODO en PDF a secretaria.sistemas.hco@udh.edu.pe

    PA->>PA: Recepciona y verifica la conformidad de los requisitos
    PA->>COM: Deriva el expediente a la Comisión

    COM->>COM: Evalúa el expediente de PPP
    COM->>COM: Designa jurado y propone fecha de sustentación
    COM->>EST: SUSTENTACIÓN (obligatoria y pública)

    alt Con observaciones
        Note over EST: 10 días hábiles para absolverlas.<br/>Incumplir ANULA la práctica (Art. 30)
        EST->>COM: Subsana
    end

    COM-->>PA: Emite constancia de aprobación de la PPP
    PA->>PA: Recepciona el informe de la Comisión
    PA->>FAC: Eleva el expediente aprobado

    FAC->>FAC: Recepciona el expediente completo
    FAC-->>EST: APRUEBA mediante RESOLUCIÓN
```

### Expediente del informe final (todo en PDF)

- [ ] Constancia de PPP de la empresa (fecha de inicio, finalización y labores realizadas)
- [ ] Ficha de evaluación emitida por el jefe de área (ANEXO 06)
- [ ] Informe de PPP según la estructura del ANEXO 05
- [ ] Ficha de supervisión y evaluación firmada y sellada por el Asesor–Supervisor (ANEXO 07)
- [ ] Formato de asistencia firmado y sellado por el representante de la institución o jefe de área
- [ ] Constancia de inscripción al sistema del área de Seguimiento del Graduado

**Condiciones de aprobación (Art. 26):** informe del Asesor–Supervisor + evaluación de la Comisión + constancia de sustentación.

**Salida del proceso:** Resolución de aprobación de PPP emitida por la Facultad de Ingeniería.

---

## 11. Convalidación de PPP por experiencia laboral

Vía excepcional para quien acredite haber laborado en labores vinculadas a la especialidad (Art. 31).

| Condición | Detalle |
|---|---|
| Ciclo | Mínimo **sexto ciclo concluido** |
| Tiempo | No menor a **16 meses** (dos años académicos) |
| Vínculo | Nombrado o contratado en institución pública o privada |
| Exposición | **Obligatoria**, coordinada con el jefe de prácticas |

### Expediente de convalidación (Art. 32)

- [ ] Solicitud al Coordinador del P.A. pidiendo la convalidación (ANEXO 08)
- [ ] Constancia(s) de trabajo y/o contrato(s) original, con tipo de trabajo, fecha de ingreso y duración
- [ ] Boletas de pago de haberes por cada institución donde laboró
- [ ] Ficha de evaluación del jefe, director o representante (con cargo y firma)
- [ ] Informe técnico según el esquema del ANEXO 09
- [ ] Recibo de pago por derecho de trámite documentario

```mermaid
sequenceDiagram
    autonumber
    actor EST as Estudiante o egresado
    participant PA as P.A. Ing. Sistemas
    participant COM as Comisión de PPP
    participant FAC as Facultad de Ingeniería

    EST->>EST: Solicita la convalidación en el sistema UDH
    EST->>PA: Envía el expediente a secretaria.sistemas.hco@udh.edu.pe
    PA->>PA: Verifica la conformidad de los requisitos
    PA->>COM: Deriva a la Comisión de PPP
    COM->>COM: Evalúa el expediente
    COM->>EST: Evalúa la exposición (obligatoria)
    COM-->>PA: Emite constancia de aprobación
    PA->>FAC: Eleva el expediente aprobado
    FAC-->>EST: APRUEBA mediante RESOLUCIÓN
```

---

## 12. Criterios adoptados donde el reglamento y el flujograma discrepan

Registrados para trazabilidad. Decisión tomada con Coordinación.

| Punto | Reglamento | Flujograma | Criterio adoptado |
|---|---|---|---|
| Orden de la carta de aceptación | Art. 7: la aceptación se emite **después** de recibir la carta de presentación | La aceptación es requisito **previo** para pedir la carta de presentación | **Flujograma** |
| Plazo del informe final | Art. 28: 20 días hábiles · Art. 38: 20 días calendarios | 20 días | **20 días hábiles** |
| Formato del informe | Art. 28: físico anillado + digital PDF y DOCX | Solo PDF por correo | **Flujograma: solo PDF** |
| Correos de destino | Solo menciona "correo institucional del P.A. / de la Comisión" | Tres correos específicos por etapa | **Flujograma** |
| Solicitud de practicante (Anexo 01) | Art. 7: la empresa la presenta antes de todo | No aparece | **Omitido** |
| Documento de autorización de la Comisión | Art. 7 inc. 2 | No aparece | **Omitido** |

> Nota: el Art. 29 remite al "Art. 24°" cuando por contenido corresponde al Art. 28. Es un error de referencia cruzada del reglamento.

---

*Última actualización: 29/09/2026 · Elaborado para el equipo del proyecto de Servicio Social*
