<script lang="ts">
  import { Button } from "$lib/components/ui/button/index.js";
  import { Label } from "$lib/components/ui/label/index.js";
  import { Input } from "$lib/components/ui/input/index.js";
  import { Textarea } from "$lib/components/ui/textarea/index.js";

  let dataIn1 = $state('');
  let dataIn2 = $state('');
  let dataOut = $state('');

  $effect(() => {
    dataIn1 = dataIn1 || (localStorage.getItem('dataIn1') || '');
    dataIn2 = dataIn2 || (localStorage.getItem('dataIn2') || '');
  });



  function formatBS(v: string) {
    const f = v.split(`\t`); 
    return `${f[0]}\t${f[1]}\t\t${f[3].replaceAll('.', '').replace(',', '.')}\t\t5. BS` 
  }
  function formatN26(v: string) {
    const f = v.split(`\t`); 
    const [y,m,d] = f[0].split('-');
    const eDate = `${d.padStart(2, '0')}/${m.padStart(2, '0')}/${y.padStart(4, '0')}`
    return `${eDate}\t${f[2] || f[5]}\t\t${f[7]}\t\t6. N26` 
  }

  function getBSDate(v: string) {
    const sDate = v.split('\t')[0]
    const [d,m,y] = sDate.split('/');
    return new Date(`${y}-${m}-${d}`);
  }
  function getN26Date(v: string) {
    return new Date(v.split(`\t`)[0]);
  }

  function parseBS() {  // From Activo Bank to Ctbl    
    dataOut = dataIn1
      .split(`\n`)      // Deconstrut lines
      .filter(v => v)   // Remove blank lines
      .sort((a,b) => getBSDate(a) < getBSDate(b) ? -1: 1) // Order by date
      .map(v => formatBS(v)) // Format it
      .join(`\n`);      // Reconstruct string
    console.log(dataOut);
    navigator.clipboard.writeText(dataOut);
  }

  function parseN26() { // From N26 download excel to Ctbl
    dataOut = dataIn2
      .split(`\n`)    // Deconstrut lines
      .slice(1)       // Remove header line
      .filter(v => v) // Remove blank lines
      .sort((a,b) => getN26Date(a) < getN26Date(b) ? -1: 1) // Order by date
      .map(v => formatN26(v)) // Format it
      .join(`\n`);    // Reconstruct string
    console.log(dataOut);
    navigator.clipboard.writeText(dataOut);
  }

  function parseAll() {
    const arr = [
      ...dataIn1        // BS data
      .split(`\n`)      // Deconstrut lines
      .filter(v => v)   // Remove blank lines
      .map(v => {       // Format it
        return { date: getBSDate(v), line: formatBS(v) };
      }),
      ...dataIn2      // N26 data
      .split(`\n`)    // Deconstrut lines
      .slice(1)       // Remove header line
      .filter(v => v) // Remove blank lines
      .map(v => {     // Format it
        const f = v.split(`\t`);
        return { date: getN26Date(v), line: formatN26(v) };
      })
    ];
    dataOut = arr
      .sort((a,b) => a.date < b.date ? -1: 1) // Order by date
      .map(v => v.line)
      .join(`\n`); // Reconstruct string
    console.log(arr);
    console.log(dataOut);
    navigator.clipboard.writeText(dataOut);
  }

  function clearData(from: 'dataIn1' | 'dataIn2') {
    if (from === 'dataIn1') { dataIn1 = ''; } else { dataIn2 = ''; }
    localStorage.removeItem(from);
  } 

  function clearAll() {
    dataIn1 = '';
    dataIn2 = '';
    dataOut = '';
    localStorage.clear();
  }



  function handleInput(event: Event, from: 'dataIn1' | 'dataIn2') {
    const target = event.target as HTMLTextAreaElement;
    const data = target?.value || '';
    localStorage.setItem(from, data);
  }

</script>


<h1>JB-CTBL-Parser</h1>

<section>
  <div class="col-1 pb-5">
    <h2>5. BS</h2>
    <!-- <textarea bind:value={dataIn1} on:input={handleInput}/> -->
    <Textarea class="mb-5" style="height: 100px;" bind:value={dataIn1} jbOnChange={(e) => handleInput(e, 'dataIn1')}/>
    <Button onclick={() => clearData('dataIn1')} variant="destructive">Clear</Button>
    <Button class="ml-2" onclick={parseBS}>Parse It</Button>
  </div>
  <div class="col-2 pb-5">
    <h2>6. N26</h2>
    <Textarea class="mb-5" style="height: 100px;" bind:value={dataIn2} jbOnChange={(e) => handleInput(e, 'dataIn2')}/>
    <Button onclick={() => clearData('dataIn2')} variant="destructive">Clear</Button>
    <Button class="ml-2" onclick={parseN26}>Parse It</Button>
  </div>
</section>


<hr/>
<h2>Output:</h2>
<!-- <Button class="mb-5 ml-5" onclick={clearAll} variant="destructive">Clear All</Button> -->
<Button class="mb-5 ml-5" onclick={parseAll}>Parse All</Button>
<pre class="result">{dataOut}</pre>



<style>
  h1 { margin: 0 auto 20px; text-align: center; font-size: 2rem; }
  section { 
    padding: 10px 30px; 
    display: flex;
  }
  .col-1, .col-2 {
    flex: 1;
    margin-right: 20px;
  }
  h2 { margin: 10px; }
  .result {
    white-space: pre;
    border: 1px solid;
    padding: 10px;
    background: #f1f1f1;
    margin: 0 25px;
  }
</style>


