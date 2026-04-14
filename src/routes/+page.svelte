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

  // Format helpers
  const pct = (v: number) => (v * 100).toFixed(2) + '%';
  const cop = (v: number) =>
    new Intl.NumberFormat('es-CO', { style: 'currency', currency: 'COP', maximumFractionDigits: 0 }).format(v);
  const num = (v: number, dec = 2) => v.toFixed(dec);

  // FCL chart helpers
  const fclMax = Math.max(...fclProyecciones.map(d => Math.abs(d.value)));
  const barHeight = (v: number) => Math.round((Math.abs(v) / fclMax) * 100);
</script>

<svelte:head>
  <title>Dashboard Financiero — Tableros & Crayolas SAS</title>
</svelte:head>

<main class="min-h-screen bg-gray-50 text-gray-800 p-6 font-sans">
  <h1 class="text-3xl font-bold text-center mb-2 text-blue-900">
    Dashboard Financiero — Tableros & Crayolas SAS
  </h1>
  <p class="text-center text-gray-500 mb-8 text-sm">Año de referencia: 2015</p>

  <!-- ── Sección 1: ROA vs WACC ─────────────────────────────────────────── -->
  <section class="bg-white rounded-2xl shadow p-6 mb-6">
    <h2 class="text-xl font-semibold mb-4 text-blue-800">Rentabilidad vs Costo de Capital</h2>
    <div class="flex items-end gap-10 justify-center h-48">
      <!-- ROA bar -->
      <div class="flex flex-col items-center gap-2">
        <span class="text-sm font-semibold text-gray-600">{pct(roa)}</span>
        <div
          class="w-20 bg-blue-400 rounded-t transition-all"
          style="height: {Math.round((roa / (wacc * 1.5)) * 160)}px"
        ></div>
        <span class="text-sm font-medium">ROA</span>
        <span class="text-xs text-gray-500">Rentabilidad del Activo</span>
      </div>
      <!-- WACC bar -->
      <div class="flex flex-col items-center gap-2">
        <span class="text-sm font-semibold text-gray-600">{pct(wacc)}</span>
        <div
          class="w-20 bg-red-400 rounded-t transition-all"
          style="height: 160px"
        ></div>
        <span class="text-sm font-medium">WACC</span>
        <span class="text-xs text-gray-500">Costo Promedio Ponderado</span>
      </div>
    </div>
    <p class="text-sm text-center text-gray-500 mt-4">
      El ROA ({pct(roa)}) está por debajo del WACC ({pct(wacc)}), lo que indica que la empresa
      no está generando valor suficiente para cubrir el costo de su capital.
    </p>
  </section>

  <!-- ── Sección 2: Proyectos de inversión ─────────────────────────────── -->
  <section class="bg-white rounded-2xl shadow p-6 mb-6">
    <h2 class="text-xl font-semibold mb-1 text-blue-800">Optimización de Portafolio de Proyectos</h2>
    <p class="text-sm text-gray-500 mb-4">
      Presupuesto disponible: {cop(150_000_000)} — Restricción de capital lineal al 8% E.A.
    </p>
    <div class="overflow-x-auto">
      <table class="w-full text-sm border-collapse">
        <thead>
          <tr class="bg-blue-50 text-left">
            <th class="px-4 py-2 border border-gray-200">Proyecto</th>
            <th class="px-4 py-2 border border-gray-200 text-right">VPN</th>
            <th class="px-4 py-2 border border-gray-200 text-right">Inversión Año 0</th>
            <th class="px-4 py-2 border border-gray-200 text-center">Seleccionado</th>
          </tr>
        </thead>
        <tbody>
          {#each projects as p}
            <tr class={p.selected ? 'bg-green-50' : 'bg-white'}>
              <td class="px-4 py-2 border border-gray-200 font-semibold">Proyecto {p.name}</td>
              <td class="px-4 py-2 border border-gray-200 text-right">{cop(p.vpn)}</td>
              <td class="px-4 py-2 border border-gray-200 text-right">{cop(p.inversion)}</td>
              <td class="px-4 py-2 border border-gray-200 text-center">
                {#if p.selected}
                  <span class="inline-block bg-green-500 text-white text-xs px-2 py-0.5 rounded-full">Sí</span>
                {:else}
                  <span class="inline-block bg-gray-300 text-gray-600 text-xs px-2 py-0.5 rounded-full">No</span>
                {/if}
              </td>
            </tr>
          {/each}
        </tbody>
        <tfoot>
          <tr class="bg-blue-50 font-semibold">
            <td class="px-4 py-2 border border-gray-200">Total Seleccionados</td>
            <td class="px-4 py-2 border border-gray-200 text-right">{cop(vpnTotal)}</td>
            <td class="px-4 py-2 border border-gray-200 text-right">{cop(-inversionUtilizada)}</td>
            <td class="px-4 py-2 border border-gray-200 text-center">C + D</td>
          </tr>
        </tfoot>
      </table>
    </div>
    <p class="text-sm text-gray-500 mt-3">
      La combinación C+D maximiza el VPN total ({cop(vpnTotal)}) con una inversión de
      {cop(inversionUtilizada)}, dentro del presupuesto de {cop(150_000_000)}.
    </p>
  </section>

  <!-- ── Sección 3: Indicadores Clave ──────────────────────────────────── -->
  <section class="bg-white rounded-2xl shadow p-6 mb-6">
    <h2 class="text-xl font-semibold mb-4 text-blue-800">Indicadores Financieros Clave (2015)</h2>
    <div class="overflow-x-auto">
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
            <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">
              {razonCorriente > 1 ? 'Activo corriente cubre el pasivo corriente — liquidez adecuada' : 'Riesgo de liquidez'}
            </td>
          </tr>
          <tr class="bg-gray-50">
            <td class="px-4 py-2 border border-gray-200 font-medium">Rotación de Cartera</td>
            <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{num(rotacionCartera, 1)} días</td>
            <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">
              {rotacionCartera > 60 ? 'Cobro lento — cartera con alta concentración de mora' : 'Rotación de cartera saludable'}
            </td>
          </tr>
          <tr class="bg-white">
            <td class="px-4 py-2 border border-gray-200 font-medium">Cobertura de Intereses</td>
            <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{num(coberturaIntereses)} veces</td>
            <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">
              {coberturaIntereses < 1 ? 'EBIT insuficiente para cubrir gastos financieros — riesgo de insolvencia' : 'Cobertura adecuada'}
            </td>
          </tr>
          <tr class="bg-gray-50">
            <td class="px-4 py-2 border border-gray-200 font-medium">ROA</td>
            <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{pct(roa)}</td>
            <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">
              Retorno sobre activos totales — por debajo del WACC ({pct(wacc)})
            </td>
          </tr>
          <tr class="bg-white">
            <td class="px-4 py-2 border border-gray-200 font-medium">WACC</td>
            <td class="px-4 py-2 border border-gray-200 text-right font-semibold">{pct(wacc)}</td>
            <td class="px-4 py-2 border border-gray-200 text-gray-500 text-xs">
              Costo promedio ponderado de capital (2015)
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>

  <!-- ── Sección 4: FCL Proyecciones 2016–2025 ─────────────────────────── -->
  <section class="bg-white rounded-2xl shadow p-6 mb-6">
    <h2 class="text-xl font-semibold mb-1 text-blue-800">Flujo de Caja Libre Proyectado (2016–2025)</h2>
    <p class="text-sm text-gray-500 mb-6">
      Valor Terminal: {cop(valorTerminal)} — VP Valor Terminal: {cop(vpValorTerminal)}
    </p>

    <!-- Bar chart with negative support -->
    <div class="flex items-end gap-1 justify-center" style="height: 220px; position: relative;">
      <!-- Zero axis -->
      <div
        class="absolute w-full border-t-2 border-gray-400"
        style="bottom: 50%; left: 0;"
      ></div>

      {#each fclProyecciones as entry}
        {@const isPositive = entry.value >= 0}
        {@const heightPx = barHeight(entry.value)}
        <div class="flex flex-col items-center flex-1 min-w-0" style="height: 220px; justify-content: center; position: relative;">
          <!-- Positive bar: grows upward from center -->
          {#if isPositive}
            <div
              class="w-full bg-blue-400 rounded-t"
              style="height: {Math.round(heightPx * 0.9)}px; position: absolute; bottom: 50%;"
              title="{entry.year}: {cop(entry.value)}"
            ></div>
          {:else}
            <!-- Negative bar: grows downward from center -->
            <div
              class="w-full bg-red-400 rounded-b"
              style="height: {Math.round(heightPx * 0.9)}px; position: absolute; top: 50%;"
              title="{entry.year}: {cop(entry.value)}"
            ></div>
          {/if}
          <!-- Year label at bottom -->
          <span
            class="text-xs text-gray-500 absolute"
            style="bottom: 4px;"
          >{entry.year}</span>
        </div>
      {/each}
    </div>

    <!-- Legend + value table -->
    <div class="flex gap-4 mt-2 mb-4 justify-center text-xs">
      <span class="flex items-center gap-1"><span class="inline-block w-3 h-3 bg-blue-400 rounded"></span>FCL positivo</span>
      <span class="flex items-center gap-1"><span class="inline-block w-3 h-3 bg-red-400 rounded"></span>FCL negativo</span>
    </div>

    <div class="overflow-x-auto">
      <table class="w-full text-xs border-collapse">
        <thead>
          <tr class="bg-blue-50">
            {#each fclProyecciones as entry}
              <th class="px-2 py-1 border border-gray-200 text-center">{entry.year}</th>
            {/each}
          </tr>
        </thead>
        <tbody>
          <tr>
            {#each fclProyecciones as entry}
              <td class={`px-2 py-1 border border-gray-200 text-right ${entry.value < 0 ? 'text-red-600' : 'text-blue-700'}`}>
                {cop(entry.value)}
              </td>
            {/each}
          </tr>
        </tbody>
      </table>
    </div>
  </section>

  <!-- ── Footer ─────────────────────────────────────────────────────────── -->
  <footer class="text-center text-xs text-gray-400 mt-8">
    Datos extraídos de prueba técnica real — Tableros & Crayolas SAS
  </footer>
</main>
