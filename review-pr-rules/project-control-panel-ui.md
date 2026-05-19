# Project — `control-panel-ui`

## Phase 2 checklist

- **Golden rule**: existing entity = 100% pre-populated form, no "Select..." placeholders for existing data
- `showToast` from `@/lib/toasts` — never import `toast` from `sonner` directly
- Icons: `lucide-react` only, no custom `strokeWidth`
- Semantic color tokens only — never `text-white`, `bg-black`, `text-gray-*`, `text-amber-*`, `bg-amber-*`, `border-amber-*`, `text-green-*`, `bg-green-*`, `border-green-*`, `hover:border-gray-*`; replacements: warning/amber → `bg-warning-10 text-warning-100`; success/green → `bg-success-light text-success`; neutral borders → `hover:border-border`; white backgrounds → `bg-background`
- **`size="default"` on all buttons** — NEVER `size="sm"` in drawers/modals, including inline remove/delete buttons (Trash2, X). Check ALL `<Button` in the diff
- API calls via React Query hooks only — never `axios` directly in components
- Drawers: `side="drawer"`, `showOverlay={false}`, `modal={false}`
- Edit forms: fields pre-populated, masks applied (CPF, CNPJ, phone, CEP, currency)
- Destructive actions use `AlertDialog` for confirmation
- `overflow-y-scroll` on main (not `auto`), `modal={false}` on Radix components
- UI text in Portuguese, code in English
- Every new file in `src/lib/api/*.ts` or `src/lib/hooks/*.ts` **must** have a corresponding `*.test.ts(x)` file — absence is WARNING
