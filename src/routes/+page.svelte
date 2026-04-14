<script lang="ts">
  import type { Project, FclEntry } from './data.ts';
  import {
    roa, wacc,
    projects,
    vpnTotal, inversionUtilizada,
    razonCorriente, rotacionCartera, coberturaIntereses,
    fclProyecciones,
    valorTerminal, vpValorTerminal
  } from './data.ts';

  const pct = (v: number): string => (v * 100).toFixed(2) + '%';

  const cop = (v: number): string =>
    new Intl.NumberFormat('es-CO', {
      style: 'currency',
      currency: 'COP',
      maximumFractionDigits: 0
    }).format(v);

  const num = (v: number, dec = 2): string => v.toFixed(dec);

  // ROA expressed as % of WACC — WACC bar is always 100% reference width
  const roaBarPct: number = (roa / wacc) * 100; // ≈ 18.2

  // FCL inline bars — widths proportional to max absolute value
  const fclMax: number = Math.max(...fclProyecciones.map((d: FclEntry) => Math.abs(d.value)));
  const fclBarPct = (v: number): number => Math.round((Math.abs(v) / fclMax) * 100);

  // Derived label
  const roaPerHundred: string = (roa / wacc * 100).toFixed(1);
</script>

<svelte:head>
  <title>Análisis Financiero — Tableros & Crayolas SAS</title>
</svelte:head>

<!-- Root wrapper: warm off-white background, DM Sans body -->
<div style="background:#f7f5f0; min-height:100vh; font-family:'DM Sans', system-ui, sans-serif; color:#0f0e0c;">
  <div class="max-w-5xl mx-auto px-4 py-8 sm:px-8 sm:py-12">

    <!-- ── Header ─────────────────────────────────────────────────────────── -->
    <header class="flex items-start justify-between gap-4 mb-8 pb-6" style="border-bottom:1px solid #d4d0c8;">
      <div>
        <h1 style="font-family:'DM Serif Display', Georgia, serif; font-size:clamp(22px,4vw,28px); color:#0f0e0c; line-height:1.15; margin:0 0 4px 0;">
          Tableros &amp; Crayolas SAS
        </h1>
        <p style="font-family:'DM Mono', 'Courier New', monospace; font-size:11px; color:#8a8780; letter-spacing:0.05em; margin:0;">
          Análisis Financiero · 2013–2025
        </p>
      </div>
      <div style="text-align:right; flex-shrink:0;">
        <p style="font-family:'DM Mono', 'Courier New', monospace; font-size:11px; color:#8a8780; margin:0 0 2px 0;">
          Tomas Estrada Echeverri
        </p>
        <p style="font-family:'DM Mono', 'Courier New', monospace; font-size:11px; color:#8a8780; margin:0;">
          Planeación Financiera · 2026
        </p>
      </div>
    </header>

    <!-- ── Verdict banner ─────────────────────────────────────────────────── -->
    <div class="mb-6 px-5 py-4" style="background:#fdf1ef; border-left:3px solid #c0392b;">
      <p style="font-family:'DM Sans', system-ui, sans-serif; font-size:14px; line-height:1.65; color:#0f0e0c; margin:0;">
        Con un ROA de
        <span style="font-family:'DM Mono', monospace; font-weight:500; color:#c0392b;">{pct(roa)}</span>
        frente a un WACC de
        <span style="font-family:'DM Mono', monospace; font-weight:500; color:#c0392b;">{pct(wacc)}</span>,
        la empresa destruye valor en cada ciclo operativo: el retorno sobre activos cubre únicamente
        <span style="font-family:'DM Mono', monospace; font-weight:500; color:#c0392b;">{roaBarPct.toFixed(1)}%</span>
        del costo de capital, señal de alerta estructural que requiere acción inmediata sobre
        eficiencia operativa y estructura de financiamiento.
      </p>
    </div>

    <!-- ── Grid: ROA vs WACC + Portfolio ─────────────────────────────────── -->
    <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">

      <!-- Card 01: ROA vs WACC -->
      <div class="bg-white p-5" style="border:1px solid #d4d0c8; border-radius:4px;">
        <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.12em; color:#8a8780; text-transform:uppercase; margin:0 0 16px 0;">
          01 · Rentabilidad vs Costo de Capital
        </p>

        <!-- WACC bar: 100% reference -->
        <div class="mb-5">
          <div class="flex items-baseline justify-between mb-1">
            <span style="font-family:'DM Sans', sans-serif; font-size:13px; font-weight:500; color:#0f0e0c;">WACC</span>
            <span style="font-family:'DM Mono', monospace; font-size:15px; font-weight:500; color:#0f0e0c;">{pct(wacc)}</span>
          </div>
          <div style="height:28px; background:#0f0e0c; border-radius:2px; width:100%;"></div>
          <p style="font-family:'DM Mono', monospace; font-size:10px; color:#8a8780; margin:4px 0 0 0;">
            Costo promedio ponderado · referencia 100%
          </p>
        </div>

        <!-- ROA bar: proportional -->
        <div class="mb-5">
          <div class="flex items-baseline justify-between mb-1">
            <span style="font-family:'DM Sans', sans-serif; font-size:13px; font-weight:500; color:#c0392b;">ROA</span>
            <span style="font-family:'DM Mono', monospace; font-size:15px; font-weight:500; color:#c0392b;">{pct(roa)}</span>
          </div>
          <div style="height:28px; background:#f7f5f0; border:1px solid #d4d0c8; border-radius:2px; position:relative; overflow:hidden;">
            <div style="height:100%; background:#c0392b; border-radius:2px; width:{roaBarPct.toFixed(2)}%;"></div>
          </div>
          <p style="font-family:'DM Mono', monospace; font-size:10px; color:#8a8780; margin:4px 0 0 0;">
            Retorno sobre activos · {roaBarPct.toFixed(1)}% del costo de capital
          </p>
        </div>

        <div style="border-top:1px solid #d4d0c8; padding-top:12px; margin-top:4px;">
          <p style="font-family:'DM Sans', sans-serif; font-size:12px; color:#8a8780; line-height:1.5; margin:0;">
            Por cada $100 de costo de capital, la empresa genera $<span style="font-family:'DM Mono',monospace;">{roaBarPct.toFixed(1)}</span> de retorno — déficit de $<span style="font-family:'DM Mono',monospace;">{(100 - roaBarPct).toFixed(1)}</span>.
          </p>
        </div>
      </div>

      <!-- Card 02: Portfolio -->
      <div class="bg-white p-5" style="border:1px solid #d4d0c8; border-radius:4px;">
        <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.12em; color:#8a8780; text-transform:uppercase; margin:0 0 16px 0;">
          02 · Optimización de Portafolio · $150M
        </p>

        <div style="margin-bottom:16px;">
          {#each projects as p (p.name)}
            <div class="flex items-center justify-between py-2" style="border-bottom:1px solid #d4d0c8;">
              <div class="flex items-center gap-2">
                <span
                  style="font-family:'DM Mono', monospace; font-size:10px; font-weight:500; width:20px; height:20px; display:flex; align-items:center; justify-content:center; border-radius:2px; flex-shrink:0; background:{p.selected ? '#1a6b47' : '#e8e4dc'}; color:{p.selected ? '#ffffff' : '#8a8780'};"
                >{p.name}</span>
                <span style="font-family:'DM Sans', sans-serif; font-size:13px; font-weight:{p.selected ? '500' : '400'}; color:{p.selected ? '#0f0e0c' : '#8a8780'};">
                  Proyecto {p.name}
                </span>
                {#if !p.selected}
                  <span style="font-family:'DM Mono', monospace; font-size:10px; color:#b0aba3; margin-left:2px;">Inv. {cop(p.inversion)}</span>
                {/if}
              </div>
              <span style="font-family:'DM Mono', monospace; font-size:13px; font-weight:500; color:{p.selected ? '#1a6b47' : '#8a8780'}; flex-shrink:0; margin-left:8px;">
                {cop(p.vpn)}
              </span>
            </div>
          {/each}
        </div>

        <!-- Optimal result callout -->
        <div style="background:#f0f7f3; border:1px solid #b8d9c9; border-radius:4px; padding:12px 14px;">
          <p style="font-family:'DM Mono', monospace; font-size:9px; letter-spacing:0.1em; color:#1a6b47; text-transform:uppercase; margin:0 0 8px 0;">
            Selección óptima · C + D · optimización lineal
          </p>
          <div class="flex justify-between items-baseline mb-1">
            <span style="font-family:'DM Sans', sans-serif; font-size:12px; color:#1a6b47;">VPN total maximizado</span>
            <span style="font-family:'DM Mono', monospace; font-size:16px; font-weight:500; color:#1a6b47;">{cop(vpnTotal)}</span>
          </div>
          <div class="flex justify-between items-baseline">
            <span style="font-family:'DM Sans', sans-serif; font-size:12px; color:#1a6b47;">Inversión utilizada</span>
            <span style="font-family:'DM Mono', monospace; font-size:12px; color:#1a6b47;">{cop(inversionUtilizada)} / {cop(150_000_000)}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- ── Ratios ──────────────────────────────────────────────────────────── -->
    <div class="bg-white mb-4" style="border:1px solid #d4d0c8; border-radius:4px;">
      <div class="px-5 pt-4 pb-3" style="border-bottom:1px solid #d4d0c8;">
        <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.12em; color:#8a8780; text-transform:uppercase; margin:0;">
          03 · Indicadores Financieros Clave · 2015
        </p>
      </div>
      <div class="grid grid-cols-1 sm:grid-cols-3">

        <!-- Razón Corriente -->
        <div class="px-5 py-5" style="border-bottom:1px solid #d4d0c8;">
          <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.08em; color:#8a8780; text-transform:uppercase; margin:0 0 8px 0;">
            Razón Corriente
          </p>
          <p style="font-family:'DM Mono', monospace; font-size:32px; font-weight:500; color:#1a6b47; line-height:1; margin:0 0 6px 0;">
            {num(razonCorriente)}<span style="font-size:16px;">×</span>
          </p>
          <p style="font-family:'DM Sans', sans-serif; font-size:12px; color:#8a8780; line-height:1.45; margin:0;">
            Activo corriente cubre el pasivo corriente. Liquidez adecuada.
          </p>
        </div>

        <!-- Rotación Cartera -->
        <div class="px-5 py-5" style="border-bottom:1px solid #d4d0c8;">
          <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.08em; color:#8a8780; text-transform:uppercase; margin:0 0 8px 0;">
            Rotación de Cartera
          </p>
          <p style="font-family:'DM Mono', monospace; font-size:32px; font-weight:500; color:#c0392b; line-height:1; margin:0 0 6px 0;">
            {num(rotacionCartera, 1)}<span style="font-size:14px;"> días</span>
          </p>
          <p style="font-family:'DM Sans', sans-serif; font-size:12px; color:#8a8780; line-height:1.45; margin:0;">
            Cobro excesivamente lento. Alta concentración de mora en cartera.
          </p>
        </div>

        <!-- Cobertura Intereses -->
        <div class="px-5 py-5">
          <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.08em; color:#8a8780; text-transform:uppercase; margin:0 0 8px 0;">
            Cobertura de Intereses
          </p>
          <p style="font-family:'DM Mono', monospace; font-size:32px; font-weight:500; color:#c0392b; line-height:1; margin:0 0 6px 0;">
            {num(coberturaIntereses)}<span style="font-size:16px;">×</span>
          </p>
          <p style="font-family:'DM Sans', sans-serif; font-size:12px; color:#8a8780; line-height:1.45; margin:0;">
            EBIT insuficiente para cubrir gastos financieros. Riesgo de insolvencia.
          </p>
        </div>

      </div>
    </div>

    <!-- ── FCL Table ───────────────────────────────────────────────────────── -->
    <div class="bg-white mb-4" style="border:1px solid #d4d0c8; border-radius:4px;">

      <!-- FCL header -->
      <div class="px-5 pt-4 pb-4" style="border-bottom:1px solid #d4d0c8;">
        <div class="flex flex-wrap items-start justify-between gap-4">
          <div>
            <p style="font-family:'DM Mono', monospace; font-size:10px; letter-spacing:0.12em; color:#8a8780; text-transform:uppercase; margin:0 0 4px 0;">
              04 · Flujo de Caja Libre Proyectado · 2016–2025
            </p>
            <h2 style="font-family:'DM Serif Display', Georgia, serif; font-size:20px; color:#0f0e0c; margin:0; font-weight:400;">
              Capacidad de Generación de Caja
            </h2>
          </div>
          <!-- Valor Terminal callout -->
          <div style="border:1px solid #d4d0c8; border-radius:4px; padding:10px 14px; text-align:right; flex-shrink:0;">
            <p style="font-family:'DM Mono', monospace; font-size:9px; letter-spacing:0.1em; color:#8a8780; text-transform:uppercase; margin:0 0 3px 0;">
              Valor Terminal
            </p>
            <p style="font-family:'DM Mono', monospace; font-size:16px; font-weight:500; color:#0f0e0c; margin:0 0 1px 0;">
              {cop(valorTerminal)}
            </p>
            <p style="font-family:'DM Mono', monospace; font-size:10px; color:#8a8780; margin:0;">
              VP: {cop(vpValorTerminal)}
            </p>
          </div>
        </div>
      </div>

      <!-- Column headers -->
      <div class="flex items-center px-5 py-2" style="border-bottom:1px solid #d4d0c8; background:#faf9f7;">
        <span style="font-family:'DM Mono', monospace; font-size:9px; letter-spacing:0.1em; color:#8a8780; text-transform:uppercase; width:48px; flex-shrink:0;">Año</span>
        <span style="font-family:'DM Mono', monospace; font-size:9px; letter-spacing:0.1em; color:#8a8780; text-transform:uppercase; width:140px; flex-shrink:0; text-align:right;">FCL</span>
        <span style="font-family:'DM Mono', monospace; font-size:9px; letter-spacing:0.1em; color:#8a8780; text-transform:uppercase; margin-left:16px;"></span>
      </div>

      <!-- FCL rows -->
      {#each fclProyecciones as entry (entry.year)}
        {@const isPositive = entry.value >= 0}
        {@const barW = fclBarPct(entry.value)}
        <div
          class="flex items-center px-5 py-3"
          style="border-bottom:1px solid #f0ede8;"
        >
          <!-- Year -->
          <span style="font-family:'DM Mono', monospace; font-size:13px; font-weight:500; color:#8a8780; width:48px; flex-shrink:0;">
            {entry.year}
          </span>
          <!-- Value -->
          <span style="font-family:'DM Mono', monospace; font-size:13px; font-weight:500; color:{isPositive ? '#0f0e0c' : '#c0392b'}; width:140px; flex-shrink:0; text-align:right;">
            {cop(entry.value)}
          </span>
          <!-- Inline bar -->
          <div style="flex:1; margin-left:16px; display:flex; align-items:center;">
            <div
              style="height:8px; border-radius:2px; background:{isPositive ? '#0f0e0c' : '#c0392b'}; opacity:{isPositive ? '1' : '0.8'}; width:{barW}%; max-width:100%; transition:width 0.3s ease;"
            ></div>
          </div>
        </div>
      {/each}

      <!-- Legend -->
      <div class="flex gap-5 px-5 py-3">
        <span class="flex items-center gap-1.5" style="font-family:'DM Mono', monospace; font-size:10px; color:#8a8780;">
          <span style="display:inline-block; width:12px; height:8px; background:#0f0e0c; border-radius:2px;"></span>
          FCL positivo
        </span>
        <span class="flex items-center gap-1.5" style="font-family:'DM Mono', monospace; font-size:10px; color:#8a8780;">
          <span style="display:inline-block; width:12px; height:8px; background:#c0392b; border-radius:2px;"></span>
          FCL negativo
        </span>
      </div>
    </div>

    <!-- ── Footer ─────────────────────────────────────────────────────────── -->
    <footer class="pt-5 mt-2" style="border-top:1px solid #d4d0c8;">
      <p style="font-family:'DM Mono', monospace; font-size:10px; color:#b0aba3; letter-spacing:0.04em; margin:0;">
        Datos extraídos de prueba técnica real · Tableros &amp; Crayolas SAS · Planeación Financiera
      </p>
    </footer>

  </div>
</div>
