# Instrucciones para Claude

## Reparto de trabajo con Codex (obligatorio)

Para cualquier tarea de programación en este repo, usa la skill
`codex-delegate` (`.claude/skills/codex-delegate/SKILL.md`):

- Claude planifica, da instrucciones concretas a Codex y revisa solo el diff
  (`git diff`); Codex escribe el código.
- Cambios triviales de pocas líneas: hazlos tú directamente.
- Si Codex se queda sin cuota, no tiene sesión iniciada o falla, Claude hace
  todo el trabajo sin preguntar, avisando al usuario en una línea.
- Los commits, push y PRs los hace siempre Claude, nunca Codex.
