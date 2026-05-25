# Project — `control-panel-ui`

Semgrep covers: `Button size="sm"` in Drawer/Modal/Dialog · raw color tokens (amber/green/text-white/bg-black/text-gray/hover:border-gray) · `import { toast } from "sonner"` · direct `axios` in component/page. Don't re-emit these from your own analysis.

Remaining checklist:

- **Golden rule**: existing entity = 100% pre-populated form. No "Select..." placeholders for existing data.
- Icons: `lucide-react` only, no custom `strokeWidth`.
- Drawer props: `side="drawer"`, `showOverlay={false}`, `modal={false}`.
- Edit forms: fields pre-populated, masks applied (CPF, CNPJ, phone, CEP, currency).
- Destructive actions use `AlertDialog` for confirmation.
- `overflow-y-scroll` on main (not `auto`); `modal={false}` on Radix components.
- UI text in Portuguese, code in English.
- Every new file in `src/lib/api/*.ts` or `src/lib/hooks/*.ts` must have a corresponding `*.test.ts(x)` — absence is WARNING.
