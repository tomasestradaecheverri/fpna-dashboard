<script lang="ts">
  import {
    roa,
    wacc,
    projects,
    vpnTotal,
    inversionUtilizada,
    razonCorriente,
    rotacionCartera,
    coberturaIntereses,
    fclProyecciones,
    valorTerminal,
    vpValorTerminal
  } from './data.ts';

  const pct = (v: number) => (v * 100).toFixed(2) + '%';
  const cop = (v: number) =>
    new Intl.NumberFormat('es-CO', { style: 'currency', currency: 'COP', maximumFractionDigits: 0 }).format(v);
  const num = (v: number, dec = 2) => v.toFixed(dec);

  const fclMax = Math.max(...fclProyecciones.map(d => Math.abs(d.value)));
  const barHeight = (v: number) => Math.round((Math.abs(v) / fclMax) * 100);

  // Short year labels for narrow bars: '16, '17 ...
  const shortYear = (y: number) => `'${String(y).slice(2)}`;
</script>

<svelte:head>
  <title>Dashboard Financiero — Tableros & Crayolas SAS</title>
</svelte:head>

<main class="min-h-screen bg-gray-50 text-gray-800 font-sans">
  <div class="max-w-4xl mx-auto px-4 py-6 sm:px-6">

    <h1 class="text-2xl sm:text-3xl font-bold text-center mb-1 text-blue-900 leading-tight">
      Dashboard Financiero
    </h1>
    <p class="text-center text-blue-700 font-medium mb-1 text-sm">Tableros & Crayolas SAS</p>
    <p class="text-center text-gray-400 mb-8 text-xs">Año de referencia: 2015</p>

    <!-- ── Sección 1: ROA vs WACC ───────────────────────────────────────── -->
    <section class="bg-white rounded-2xl shadow p-4 sm:p-6 mb-5">
      <h2 class="text-base sm:text-xl font-semibold mb-4 text-blue-800">Rentabilidad vs Costo de Capital</h2>
      <div class="flex items-end gap-8 sm:gap-16 justify-center" style="height: 180px;">
        <div class="flex flex-col items-center gap-1">
          <span class="text-sm font-bold text-blue-600">{pct(roa)}</span>
          <div
            class="w-16 sm:w-20 bg-blue-400 rounded-t"
            style="height: {Math.round((roa / (wacc * 1.5)) * 140)}px"
          ></div>
          <span class="text-sm font-semibold mt-1">ROA</span>
          <span class="text-xs text-gray-400 text-center">Rentabilidad<br>del Activo</span>
        </div>
        <div class="flex flex-col items-center gap-1">
          <span class="text-sm font-bold text-red-500">{pct(wacc)}</span>
          <div class="w-16 sm:w-20 bg-red-400 rounded-t" style="height: 140px"></div>
          <span class="text-sm font-semibold mt-1">WACC</span>
          <span class="text-xs text-gray-400 text-center">Costo Prom.<br>Ponderado</span>
        </div>
      </div>
      <p class="text-xs sm:text-sm text-center text-gray-500 mt-4 leading-relaxed">
        El ROA ({pct(roa)}) está por debajo del WACC ({pct(wacc)}): la empresa no genera valor
        suficiente para cubrir el costo de su capital.
      </p>
    </section>

    <!-- ── Sección 2: Proyectos ─────────────────────────────────────────── -->
    <section class="bg-white rounded-2xl shadow p-4 sm:p-6 mb-5">
      <h2 class="text-base sm:text-xl font-semibold mb-1 text-blue-800">Optimización de Portafolio</h2>
      <p class="text-xs text-gray-400 mb-4">
        Presupuesto: {cop(150_000_000)} · Tasa requerida 8% E.A.
      </p>
      <div class="overflow-x-auto -mx-4 sm:mx-0">
        <table class="min-w-full text-sm border-collapse">
          <thead>
            <tr class="bg-blue-50 text-left">
              <th class="px-4 py-2 border border-gray-200 whitespace-nowrap">Proyecto</th>
              <th class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">VPN</th>
              <th class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">Inversión</th>
              <th class="px-4 py-2 border border-gray-200 text-center whitespace-nowrap">¿Ejecutar?</th>
            </tr>
          </thead>
          <tbody>
            {#each projects as p}
              <tr class={p.selected ? 'bg-green-50' : 'bg-white'}>
                <td class="px-4 py-2 border border-gray-200 font-semibold whitespace-nowrap">Proyecto {p.name}</td>
                <td class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">{cop(p.vpn)}</td>
                <td class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">{cop(p.inversion)}</td>
                <td class="px-4 py-2 border border-gray-200 text-center">
                  {#if p.selected}
                    <span class="inline-block bg-green-500 text-white text-xs px-2 py-0.5 rounded-full">Sí</span>
                  {:else}
                    <span class="inline-block bg-gray-200 text-gray-500 text-xs px-2 py-0.5 rounded-full">No</span>
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
          <tfoot>
            <tr class="bg-blue-50 font-semibold">
              <td class="px-4 py-2 border border-gray-200 whitespace-nowrap">Total (C + D)</td>
              <td class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">{cop(vpnTotal)}</td>
              <td class="px-4 py-2 border border-gray-200 text-right whitespace-nowrap">{cop(-inversionUtilizada)}</td>
              <td class="px-4 py-2 border border-gray-200 text-center">✓</td>
            </tr>
          </tfoot>
        </table>
      </div>
      <p class="text-xs text-gray-400 mt-3">
        C+D maximiza el VPN total ({cop(vpnTotal)}) usando {cop(inversionUtilizada)} del presupuesto disponible.
      </p>
    </section>

    <!-- ── Sección 3: Indicadores ───────────────────────────────────────── -->
    <section class="bg-white rounded-2xl shadow p-4 sm:p-6 mb-5">
      <h2 class="text-base sm:text-xl font-semibold mb-4 text-blue-800">Indicadores Financieros Clave (2015)</h2>
      <!-- Card layout on mobile, table on sm+ -->
      <div class="space-y-2 sm:hidden">
        {#each [
          { label: 'Razón Corriente', value: num(razonCorriente) + ' veces', note: 'Liquidez adecuada — activo corriente cubre el pasivo corriente', ok: razonCorriente > 1 },
          { label: 'Rotación de Cartera', value: num(rotacionCartera, 1) + ' días', note: 'Cobro lento — alta concentración de mora', ok: rotacionCartera <= 60 },
          { label: 'Cobertura de Intereses', value: num(coberturaIntereses) + ' veces', note: 'EBIT insuficiente — riesgo de insolvencia', ok: coberturaIntereses >= 1 },
          { label: 'ROA', value: pct(roa), note: 'Por debajo del WACC (' + pct(wacc) + ')', ok: false },
          { label: 'WACC', value: pct(wacc), note: 'Costo promedio ponderado de capital', ok: true },
        ] as row}
          <div class="flex items-start justify-between rounded-xl border border-gray-100 px-3 py-2.5 bg-gray-50">
            <div>
              <p class="text-sm font-medium text-gray-700">{row.label}</p>
              <p class="text-xs text-gray-400 mt-0.5">{row.note}</p>
            </div>
            <span class={`text-sm font-bold ml-4 shrink-0 ${row.ok ? 'text-green-600' : 'text-red-500'}`}>
              {row.value}
            </span>
          </div>
        {/each}
      </div>
      <div class="hidden sm:block overflow-x-auto">
        <table class="w-full text-sm border-collapse">
          <thead>
            <tr class="bg-blue-50 text-left">
              <th class="px-4 py-2 border border-gray-200">Indicador</th>
              <th class="px-4 py-2 border border-gray-200 text-right">Valor</th>
              <th class="px-4 py-2 border border-gray-200">Interpretación</th>
            </tr>
          </thead>
          <tbody>
            <tr class="bg-white">
              <td class="px-4 py-2 border border-gray-200 font-medium">Razón Corriente</td>
              <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{num(razonCorriente)} veces</td>
              <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">Activo corriente cubre el pasivo corriente — liquidez adecuada</td>
            </tr>
            <tr class="bg-gray-50">
              <td class="px-4 py-2 border border-gray-200 font-medium">Rotación de Cartera</td>
              <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{num(rotacionCartera, 1)} días</td>
              <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">Cobro lento — cartera con alta concentración de mora</td>
            </tr>
            <tr class="bg-white">
              <td class="px-4 py-2 border border-gray-200 font-medium">Cobertura de Intereses</td>
              <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{num(coberturaIntereses)} veces</td>
              <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">EBIT insuficiente para cubrir gastos financieros — riesgo de insolvencia</td>
            </tr>
            <tr class="bg-gray-50">
              <td class="px-4 py-2 border border-gray-200 font-medium">ROA</td>
              <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{pct(roa)}</td>
              <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">Retorno sobre activos totales — por debajo del WACC ({pct(wacc)})</td>
            </tr>
            <tr class="bg-white">
              <td class="px-4 py-2 border border-gray-200 font-medium">WACC</td>
              <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{pct(wacc)}</td>
              <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">Costo promedio ponderado de capital (2015)</td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>

    <!-- ── Sección 4: FCL ───────────────────────────────────────────────── -->
    <section class="bg-white rounded-2xl shadow p-4 sm:p-6 mb-5">
      <h2 class="text-base sm:text-xl font-semibold mb-1 text-blue-800">Flujo de Caja Libre Proyectado (2016–2025)</h2>
      <p class="text-xs text-gray-400 mb-5">
        Valor Terminal: {cop(valorTerminal)} &nbsp;·&nbsp; VP Valor Terminal: {cop(vpValorTerminal)}
      </p>

      <!-- Chart: scrollable on very small screens -->
      <div class="overflow-x-auto -mx-4 sm:mx-0">
        <div style="min-width: 300px; height: 200px; position: relative; padding: 0 16px;">
          <!-- Zero axis -->
          <div class="absolute border-t-2 border-gray-300" style="top: 50%; left: 16px; right: 16px;"></div>

          <div class="flex items-stretch gap-1 h-full">
            {#each fclProyecciones as entry}
              {@const isPositive = entry.value >= 0}
              {@const heightPct = barHeight(entry.value)}
              <div class="flex flex-col flex-1 items-center" style="position: relative; height: 200px;">
                <!-- Value label on tap/hover area -->
                {#if isPositive}
                  <div
                    class="w-full bg-blue-400 hover:bg-blue-500 rounded-t cursor-default"
                    style="height: {Math.round(heightPct * 0.85)}%; position: absolute; bottom: 50%;"
                    title="{entry.year}: {cop(entry.value)}"
                  ></div>
                  <span
                    class="text-blue-700 font-semibold absolute"
                    style="font-size: 9px; bottom: calc(50% + {Math.round(heightPct * 0.85)}% + 2px); white-space: nowrap;"
                  >{cop(entry.value).replace('$\u00a0', '$').replace('COP\u00a0','')}</span>
                {:else}
                  <div
                    class="w-full bg-red-400 hover:bg-red-500 rounded-b cursor-default"
                    style="height: {Math.round(heightPct * 0.85)}%; position: absolute; top: 50%;"
                    title="{entry.year}: {cop(entry.value)}"
                  ></div>
                  <span
                    class="text-red-600 font-semibold absolute"
                    style="font-size: 9px; top: calc(50% + {Math.round(heightPct * 0.85)}% + 2px); white-space: nowrap;"
                  >{cop(entry.value).replace('$\u00a0', '$').replace('COP\u00a0','')}</span>
                {/if}
                <!-- Year label -->
                <span
                  class="text-gray-500 absolute font-medium"
                  style="font-size: 10px; bottom: 2px;"
                >{shortYear(entry.year)}</span>
              </div>
            {/each}
          </div>
        </div>
      </div>

      <!-- Legend -->
      <div class="flex gap-4 mt-3 justify-center text-xs text-gray-500">
        <span class="flex items-center gap-1"><span class="inline-block w-3 h-3 bg-blue-400 rounded-sm"></span>FCL positivo</span>
        <span class="flex items-center gap-1"><span class="inline-block w-3 h-3 bg-red-400 rounded-sm"></span>FCL negativo</span>
      </div>

      <!-- FCL value table: 2-col on mobile, 10-col on sm+ -->
      <div class="mt-5">
        <!-- Mobile: vertical list -->
        <div class="grid grid-cols-2 gap-1 sm:hidden">
          {#each fclProyecciones as entry}
            <div class={`flex justify-between items-center rounded-lg px-3 py-1.5 text-xs ${entry.value < 0 ? 'bg-red-50' : 'bg-blue-50'}`}>
              <span class="font-medium text-gray-600">{entry.year}</span>
              <span class={`font-semibold ${entry.value < 0 ? 'text-red-600' : 'text-blue-700'}`}>
                {cop(entry.value)}
              </span>
            </div>
          {/each}
        </div>
        <!-- Desktop: horizontal table -->
        <div class="hidden sm:block overflow-x-auto">
          <table class="w-full text-xs border-collapse">
            <thead>
              <tr class="bg-blue-50">
                {#each fclProyecciones as entry}
                  <th class="px-2 py-1 border border-gray-200 text-center whitespace-nowrap">{entry.year}</th>
                {/each}
              </tr>
            </thead>
            <tbody>
              <tr>
                {#each fclProyecciones as entry}
                  <td class={`px-2 py-1 border border-gray-200 text-right whitespace-nowrap ${entry.value < 0 ? 'text-red-600' : 'text-blue-700'}`}>
                    {cop(entry.value)}
                  </td>
                {/each}
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </section>

    <footer class="text-center text-xs text-gray-400 mt-6 pb-4">
      Datos extraídos de prueba técnica real — Tableros & Crayolas SAS
    </footer>

  </div>
</main>
