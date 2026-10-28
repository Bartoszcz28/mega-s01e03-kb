<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  // Counted the desk's way: where the tenant files each report. Reports the desk read as a
  // one-day move stay under their cause and are footnoted.
  const count = (cause: string) => merged.filter((r) => causeOf(r, tenantOf(r)) === cause).length;
  const moved = $derived(merged.filter((r) => notes.oneDayMove.includes(r.id)).length);
  const rows = $derived([
    { problem: 'parser reads dates month first', cause: 'parser', n: count('parser') },
    { problem: 'booking-v3 form hint says MM/DD/YYYY', cause: 'hint', n: count('hint') },
  ]);
</script>

<p><strong>{merged.length}</strong> reports merged into #{ticket} · <strong>{rows.length}</strong> problems</p>

<table>
  <thead><tr><th>problem</th><th>cause</th><th>reports</th></tr></thead>
  <tbody>
    {#each rows as row (row.cause)}
      <tr><td>{row.problem}</td><td><code>{row.cause}</code></td><td>{row.n}</td></tr>
    {/each}
  </tbody>
</table>

<p class="note">{moved} of the parser reports read as a one-day move; filed under <code>parser</code> by tenant.</p>
