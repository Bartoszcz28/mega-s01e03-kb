<script lang="ts">
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));
  // A report the desk read as a one-day move is its own problem, whatever its tenant says.
  const oneDay = (r: Report) => notes.oneDayMove.includes(r.id);
  const count = (pick: (r: Report) => boolean) => merged.filter(pick).length;
  const rows = $derived([
    { problem: 'parser reads dates month first', cause: 'parser', n: count((r) => !oneDay(r) && causeOf(r, tenantOf(r)) === 'parser') },
    { problem: 'booking-v3 form hint says MM/DD/YYYY', cause: 'hint', n: count((r) => !oneDay(r) && causeOf(r, tenantOf(r)) === 'hint') },
    { problem: 'date lands exactly one day off', cause: 'one-day', n: count(oneDay) },
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
