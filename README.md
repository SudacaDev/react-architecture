# React Architecture

Arquitectura basada en features para proyectos React escalables. Compatible con **Next.js App Router**, **Next.js Pages Router** y **Vite + React Router**.

---

## Estructura de carpetas

```
src/
├── pages/               # Route shells (solo importan la feature, sin lógica)
├── features/            # ⭐ Núcleo de la app — todo lo específico de cada página
├── components/
│   └── ui/              # Componentes genéricos reutilizables en todo el sitio
├── hooks/               # Hooks globales compartidos entre features
├── api/                 # Llamadas HTTP (fetch/axios)
├── services/            # Lógica de negocio que orquesta las llamadas a la API
├── store/               # Estado global (Zustand, Redux, Context)
├── routes/              # Configuración del router (solo Vite)
├── utils/               # Funciones utilitarias puras
└── assets/              # Imágenes, fuentes, íconos, SVGs
```

---

## La regla principal: `features/` vs `components/`

Esta es la decisión más importante de la arquitectura. La pregunta a hacerse siempre es:

> **¿Este código se usa en más de una página o feature?**
> - **Sí** → va en `components/`, `hooks/`, `utils/` (nivel global)
> - **No** → va dentro de la carpeta de la feature

### `components/ui/` — Componentes globales

Son piezas de UI **genéricas**, sin conocimiento del dominio de negocio. Se pueden usar en cualquier parte del sitio.

```
components/
└── ui/
    ├── Header/
    │   └── index.tsx       ✅ Se usa en todas las páginas
    ├── Button/
    │   └── index.tsx       ✅ Botón reutilizable en cualquier contexto
    ├── Modal/
    │   └── index.tsx       ✅ Modal genérico
    ├── Input/
    │   └── index.tsx       ✅ Input de formulario base
    └── Spinner/
        └── index.tsx       ✅ Loader global
```

### `features/<name>-page/` — Todo lo específico de una página

Contiene **todo** lo que pertenece exclusivamente a esa feature: componentes, hooks, tipos, validaciones. Nada de esto debe importarse desde otra feature.

```
features/
└── finance-stock-page/
    ├── index.tsx                          # Componente raíz de la feature
    ├── components/
    │   ├── FormFinanceStock.tsx           ✅ Solo se usa en esta feature
    │   ├── StockChart.tsx                 ✅ Solo se usa en esta feature
    │   └── StockSummaryCard.tsx           ✅ Solo se usa en esta feature
    ├── hooks/
    │   └── use-finance-stock.ts           ✅ Hook específico de esta feature
    ├── types/
    │   └── finance-stock.types.ts         ✅ Interfaces/types de esta feature
    └── validations/
        └── finance-stock.schema.ts        ✅ Schema de validación (zod, yup…)
```

---

## La misma lógica aplica a hooks, types y validaciones

| ¿Se usa en...? | Dónde va |
|----------------|----------|
| Solo una feature | `features/<name>-page/hooks/` |
| Solo una feature | `features/<name>-page/types/` |
| Solo una feature | `features/<name>-page/validations/` |
| Dos o más features | `hooks/` |
| Dos o más features | `types/` (raíz de src) |
| Dos o más features | `utils/` |

### Ejemplo: hooks

```
hooks/
└── use-auth.ts              ✅ Se usa en múltiples features (dashboard, settings, profile…)

features/
└── login-page/
    └── hooks/
        └── use-login-form.ts  ✅ Solo se usa en el formulario de login
```

### Ejemplo: types / interfaces

```
types/                        (nivel global)
└── user.types.ts            ✅ El tipo User se usa en toda la app

features/
└── finance-stock-page/
    └── types/
        └── finance-stock.types.ts  ✅ StockPosition, StockFilter, etc. solo usados acá
```

### Ejemplo: validaciones

```
features/
└── login-page/
    └── validations/
        └── login.schema.ts   ✅ Solo valida el formulario de login

features/
└── register-page/
    └── validations/
        └── register.schema.ts  ✅ Solo valida el formulario de registro
```

---

## El patrón page → feature

Las páginas (`pages/`) son **shells vacíos**. Su único trabajo es importar y renderizar la feature correspondiente. Toda la lógica real vive en `features/`.

```
pages/finance-stock/page.tsx
        ↓ importa
features/finance-stock-page/index.tsx
        ↓ usa internamente
features/finance-stock-page/components/FormFinanceStock.tsx
features/finance-stock-page/hooks/use-finance-stock.ts
features/finance-stock-page/types/finance-stock.types.ts
features/finance-stock-page/validations/finance-stock.schema.ts
```

**`pages/finance-stock/page.tsx`**
```tsx
import { FinanceStockPage } from "@/features/finance-stock-page";

export default function FinanceStockRoute() {
  return <FinanceStockPage />;
}
```

**`features/finance-stock-page/index.tsx`**
```tsx
import { FormFinanceStock } from "./components/FormFinanceStock";
import { StockChart } from "./components/StockChart";
import { useFinanceStock } from "./hooks/use-finance-stock";

export function FinanceStockPage() {
  const { stocks, isLoading } = useFinanceStock();

  return (
    <main>
      <FormFinanceStock />
      <StockChart data={stocks} isLoading={isLoading} />
    </main>
  );
}
```

---

## Árbol completo de una feature real

```
features/
└── finance-stock-page/
    ├── index.tsx                          # Exporta FinanceStockPage
    ├── components/
    │   ├── FormFinanceStock.tsx
    │   ├── StockChart.tsx
    │   └── StockSummaryCard.tsx
    ├── hooks/
    │   └── use-finance-stock.ts
    ├── types/
    │   └── finance-stock.types.ts
    └── validations/
        └── finance-stock.schema.ts
```

---

## Resumen visual

```
¿Es genérico y se reutiliza en todo el sitio?
│
├── SÍ ──► components/ui/Header/index.tsx
│          components/ui/Button/index.tsx
│          hooks/use-auth.ts
│          types/user.types.ts
│
└── NO ──► features/finance-stock-page/components/FormFinanceStock.tsx
           features/finance-stock-page/hooks/use-finance-stock.ts
           features/finance-stock-page/types/finance-stock.types.ts
           features/finance-stock-page/validations/finance-stock.schema.ts
```

---

## Reglas de oro

1. **`pages/` nunca tiene lógica** — solo importa y renderiza la feature.
2. **Una feature no importa de otra feature** — si necesitan compartir algo, ese algo sube a nivel global.
3. **Antes de crear un componente global, preguntá si realmente se va a reutilizar** — si no estás seguro, empezá dentro de la feature y subilo después.
4. **El nombre de la feature siempre termina en `-page`** — `login-page/`, `dashboard-page/`, `finance-stock-page/`.
5. **El barrel export de la feature es solo `index.tsx`** — nada desde afuera importa los internos directamente.