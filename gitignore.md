# Co je `.gitignore` a k čemu slouží?

`.gitignore` je speciální soubor, který určuje, které soubory a složky by měly být **ignorovány** (necommitovány) při práci s Gitem.

## K čemu slouží `.gitignore`?

1. **Vynechání nepotřebných souborů** - zabraňuje commitování dočasných souborů, závislostí a systémových souborů
2. **Zabezpečení** - zabraňuje nechtěnému commitování citlivých dat (hesla, API klíče)
3. **Optimalizace** - zmenšuje velikost repozitáře vynecháním velkých nebo generovaných souborů

## Co typicky patří do `.gitignore`?

Pro Vite/React/Node.js projekt (jako ten ve vašem `package.json`) by `.gitignore` měl obsahovat:

```
# Závislosti
node_modules/
dist/
.temp/
.cache/

# IDE a editorové soubory
.vscode/
.idea/
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Systémové soubory
.DS_Store
Thumbs.db

# Vite a build soubory
.env
.env.local
.env.development
.env.test
.env.production
.vite/
*.local

# Logy a debug soubory
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# Testovací soubory
coverage/
.nyc_output/

# Typové soubory
*.tsbuildinfo
```

## Proč ignorovat `.vite/`?

Složka `.vite/` obsahuje:
- Cache Vite pro rychlejší vývoj
- Dočasné build soubory
- Optimalizované závislosti
Tyto soubory se generují automaticky při každém spuštění `vite dev` a nemají být součástí repozitáře.

## Jak vytvořit `.gitignore`?

1. V kořenové složce projektu vytvořte soubor `.gitignore`
2. Vložte do něj výše uvedený obsah
3. Uložte soubor

Git pak tyto soubory a složky automaticky ignoruje při všech operacích.
