# Javier Oliveros Lepe
# Facturación WillmanTech — Informe y Exportación UBL

## Descripción general

Este proyecto contiene dos archivos XML que implementan el ciclo completo de facturación para **WillmanTech S.L.**:

| Archivo | Propósito |
|---|---|
| `report_invoice_willmantech.xml` | Plantilla de informe visual (PDF) para Odoo |
| `invoice_ubl.xml` | Factura electrónica en formato UBL 2.1 / PEPPOL BIS 3 |

---

## 1. Plantilla de informe — `report_invoice_willmantech.xml`

### Descripción
Plantilla QWeb de Odoo que genera el PDF imprimible de las facturas de cliente. Se integra con el layout corporativo externo (`web.external_layout`) y soporta facturas, borradores y facturas canceladas.

### Funcionalidades
- **Dirección de envío:** muestra automáticamente la dirección de envío si es distinta a la del cliente, o la omite si coinciden.
- **Cabecera de factura:** título dinámico según estado (`Factura`, `Borrador de Factura`, `Factura Cancelada`, `PROFORMA`).
- **Bloque de información:** fecha de factura, fecha de vencimiento y origen del pedido.
- **Tabla de líneas:** descripción, cantidad, unidad de medida, precio unitario, descuento (columna condicional), impuestos e importe.
- **Totales:** total neto de la factura.
- **Condiciones de pago:** nota del término de pago si está configurada.

### Uso en Odoo
Este archivo se instala como parte de un módulo Odoo personalizado. El nombre de la plantilla es `report_invoice_willmantech`.

```xml
<!-- Llamada desde la acción de informe del módulo -->
<t t-call="report_invoice_willmantech"/>
```

---

## 2. Factura electrónica — `invoice_ubl.xml`

### Descripción
Factura electrónica estructurada en **UBL 2.1**, conforme al estándar europeo **EN 16931** y al perfil **PEPPOL BIS Billing 3.0**. Apta para facturación electrónica B2B y envío a plataformas de intercambio como FACeB2B o la red PEPPOL.

### Datos de la factura

| Campo | Valor |
|---|---|
| Número | INV/2026/00042 |
| Fecha de emisión | 21/05/2026 |
| Tipo | Factura comercial (código 380) |
| Moneda | EUR |

### Partes

| Rol | Empresa | CIF | Ciudad |
|---|---|---|---|
| Emisor | WillmanTech S.L. | ESB12345678 | Sevilla |
| Receptor | Innovaciones Tecnológicas Sur S.A. | ESA87654321 | Málaga |

### Líneas de factura

| Concepto | Cantidad | Precio/h | Base imponible |
|---|---|---|---|
| Consultoría de Implantación e Infraestructura Cloud Dockerizada | 10 h | 100,00 € | 1.000,00 € |

### Resumen fiscal

| Concepto | Importe |
|---|---|
| Base imponible | 1.000,00 € |
| IVA (21 %) | 210,00 € |
| **Total a pagar** | **1.210,00 €** |

### Estándares aplicados
- `urn:cen.eu:en16931:2017` — Norma europea de factura electrónica
- `urn:fdc:peppol.eu:poacc:trns:invoice:3` — Perfil PEPPOL BIS Billing 3.0

---

## Relación entre los archivos

```
Odoo (módulo de facturación)
│
├── report_invoice_willmantech.xml   → Renderiza el PDF para el cliente
│
└── invoice_ubl.xml                  → Exportación estructurada para
                                       intercambio electrónico (PEPPOL / FACeB2B)
```

El flujo típico es:

1. Se crea la factura en Odoo.
2. Se imprime o envía como PDF usando la plantilla `report_invoice_willmantech`.
3. Se exporta en formato UBL para su envío electrónico a través de la red PEPPOL o portales de facturación electrónica.

---

## Requisitos

- **Odoo 16/17** (o superior) para la plantilla de informe.
- Validador UBL para verificar `invoice_ubl.xml` (p. ej. [PEPPOL Validator](https://docs.peppol.eu/poacc/billing/3.0/)).

---

## Notas

- Los campos de CIF/NIF se adaptan al país mediante `account_fiscal_country_id.vat_label`; en España se muestra `CIF/NIF` por defecto.
- La columna de descuento en la tabla de líneas aparece solo si alguna línea tiene descuento aplicado.
