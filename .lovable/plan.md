# ROUT overdracht: vervolgstappen 1-3 uit het dossier

## Gecontroleerd tegen de code (afwijkingen van het dossier)
- Checksum `db/0294_eudi_wallet.sql` klopt: `ede24dc6...7127`.
- 0294 maakt `oid4vci_offers`, `wallet_credentials`, `oid4vp_requests` aan — niet de `eudi_*`-tabellen die het dossier noemt.
- De test heet `src/test/migration-0294.test.ts` (niet `-contract`).
- `CODE_SCHEMA_LEVEL` staat al op `0294`; Neon staat op `0293`.
- `scripts/migration-rollout.ts` vraagt een eigenaarsadres (`MIGRATION_URL`) dat niet in deze omgeving staat — en dat ik daar ook niet wil opslaan (regel: niets bij Lovable).

## Stap 1 — Migratie 0294 uitrollen (alleen met jouw GO)
1. Lokaal eerst: `migration-0294.test.ts` en `migration-rollout.test.ts` draaien (replay op PGlite).
2. Dry run van het rollout-script (checksum + voorganger 0293 controleren).
3. Uitvoering, één van twee wegen:
   - A (aanbevolen, eigen infra): jij draait `bun scripts/migration-rollout.ts --apply db/0294_eudi_wallet.sql` zelf met je eigen eigenaarsadres.
   - B: ik voer exact dezelfde bestandstekst in één transactie uit via de tijdelijke Neon-koppeling van dit gesprek, inclusief checksumcontrole, registratie in `schema_migrations` en de definer-guard.
4. Nacontrole (alleen lezen): 0294 geregistreerd, tabellen en `rout_vci_*`/`rout_vp_*`-functies aanwezig, search_path en EXECUTE-rechten conform regels, health toont DB = 0294.

## Stap 2 — Tests voor deel 3
Nieuwe tests naast de bestaande in `src/test/`:
- ML-DSA-65 + Ed25519 hybride verificatie tegen de NIST ACVP-vectoren (eerst nagaan of die fixtures echt bestaan).
- OID4VCI: aanbod → token → credential met holder key-binding; foute proof wordt geweigerd.
- OID4VP: DCQL-match en mismatch.
- Status List: bit zetten/lezen, ingetrokken credential faalt.
Daarna de volledige suite draaien en uitslag rapporteren (geen aantallen beloven vooraf).

## Stap 3 — Documentatie
- `docs/HANDOVER.md`: correctie 0293 → 0294 alleen in de appendix (genummerde secties blijven letterlijk).
- `roadmap.md`: EUDI/PQ-mijlpalen afvinken zodra stap 1 en 2 groen zijn.

## Stap 4 — Buiten scope
IBAN/BIC, Stripe-webhooksecret, bunq-sleutels: pas bij livegang, op je eigen hosting.
