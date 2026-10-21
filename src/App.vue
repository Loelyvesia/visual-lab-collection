<script setup lang="ts">
import { computed, ref } from 'vue'

interface Project {
  slug: string
  zh: string
  en: string
  desc: string
  tags: string[]
  type: 'heat' | 'belt' | 'dial' | 'arc' | 'sun' | 'bubble' | 'timeline' | 'map'
  c: [string, string]
}

const projects: Project[] = [
  { slug: 'cosmic-abundance', zh: '宇宙元素丰度', en: 'Cosmic Abundance', desc: '周期表热力图 + 丰度散点，探索元素的相对丰度。', tags: ['分布', '类别'], type: 'heat', c: ['#6ee7ff', '#4a36ff'] },
  { slug: 'coffee-belt', zh: '咖啡带', en: 'The Coffee Belt', desc: '回归线间的产地分布 + 风味雷达，比较咖啡产国。', tags: ['空间', '分布'], type: 'belt', c: ['#a86a3a', '#c98e4f'] },
  { slug: 'rhythm-spectrum', zh: '节律光谱', en: 'Rhythm Spectrum', desc: 'BPM 刻度盘 + 能量散点，试听流派节拍。', tags: ['关系', '音频'], type: 'dial', c: ['#ff4fd8', '#4fd1ff'] },
  { slug: 'migration-paths', zh: '候鸟迁徙', en: 'Migration Paths', desc: '纬度航线板 + 月份窗口，追踪候鸟的年度旅程。', tags: ['空间', '时间'], type: 'arc', c: ['#2f6fb8', '#c2543f'] },
  { slug: 'language-families', zh: '语言星谱', en: 'Language Families', desc: '语系环状分区 + 排名条，下钻语言谱系。', tags: ['层级', '分布'], type: 'sun', c: ['#c84b31', '#2d5f6e'] },
  { slug: 'metro-atlas', zh: '地铁脉络', en: 'Metro Atlas', desc: '可切换坐标的气泡散点 + 累计增长，比较地铁系统。', tags: ['关系', '时间'], type: 'bubble', c: ['#e8641b', '#2f6fb8'] },
  { slug: 'science-pioneers', zh: '科学先驱', en: 'Science Pioneers', desc: '跨世纪时间线 + 领域分布，回望女性科学先驱。', tags: ['时间', '类别'], type: 'timeline', c: ['#8f2f2a', '#7a5aa0'] },
  { slug: 'aeolus-climate', zh: '风之城', en: 'Aeolus', desc: '虚构星球星图 + 全年气候曲线（模拟数据）。', tags: ['空间', '时间'], type: 'map', c: ['#4fd1c5', '#e8641b'] },
]

const tags = ['全部', '分布', '空间', '时间', '层级', '关系', '类别', '音频']
const active = ref('全部')
const filtered = computed(() => active.value === '全部' ? projects : projects.filter(p => p.tags.includes(active.value)))

function url(p: Project) { return `https://loelyvesia.github.io/${p.slug}/` }
</script>

<template>
  <main class="gallery">
    <header class="head">
      <p class="kicker">Visual Lab Collection</p>
      <h1>数据可视化<br />作品集</h1>
      <p class="lede">
        八件风格独立、数据驱动的交互式可视化作品。每件都含真实或明确标注的本地数据、
        两种互补视图与至少一个会真正改变数据表达的交互。
      </p>
    </header>

    <nav class="filters" aria-label="作品标签筛选">
      <button v-for="t in tags" :key="t" :class="{ on: active === t }" @click="active = t">{{ t }}</button>
    </nav>

    <section class="grid">
      <a v-for="p in filtered" :key="p.slug" class="card" :href="url(p)" target="_blank" rel="noopener">
        <div class="cover" :style="{ background: `linear-gradient(135deg, ${p.c[0]}22, ${p.c[1]}22)` }">
          <svg viewBox="0 0 200 120" class="cover-svg" aria-hidden="true">
            <template v-if="p.type === 'heat'">
              <g v-for="(r, ri) in 4" :key="ri"><g v-for="(c, ci) in 8" :key="ci">
                <rect :x="20+ci*20" :y="18+ri*22" width="16" height="16" rx="3" :fill="p.c[0]" :opacity="0.18+((ri*8+ci)%9)*0.09" />
              </g></g>
            </template>
            <template v-else-if="p.type === 'belt'">
              <rect x="18" y="48" width="164" height="28" rx="14" fill="none" :stroke="p.c[1]" opacity="0.5" />
              <circle v-for="(d, i) in [34,60,86,112,138,164]" :key="i" :cx="d" :cy="62" :r="3+ (i%4)" :fill="p.c[0]" />
            </template>
            <template v-else-if="p.type === 'dial'">
              <circle cx="100" cy="60" r="44" fill="none" :stroke="p.c[0]" opacity="0.3" />
              <circle cx="100" cy="60" r="30" fill="none" :stroke="p.c[1]" opacity="0.4" />
              <path d="M100,60 L128,42 A46,46 0 0 1 138,66 Z" :fill="p.c[0]" opacity="0.7" />
            </template>
            <template v-else-if="p.type === 'arc'">
              <path v-for="(y, i) in [28,46,64,82]" :key="i" :d="`M20,${y} C70,${y-18} 130,${y+18} 180,${y}`" fill="none" :stroke="p.c[0]" :stroke-width="i===1?3:1.5" :opacity="i===1?1:0.55" />
              <circle cx="70" cy="50" r="4" :fill="p.c[1]" />
            </template>
            <template v-else-if="p.type === 'sun'">
              <g v-for="(seg, i) in 6" :key="i">
                <path :d="`M100,60 L${100+38*Math.cos(i*Math.PI/3)},${60+38*Math.sin(i*Math.PI/3)} A38,38 0 0 1 ${100+38*Math.cos((i+1)*Math.PI/3)},${60+38*Math.sin((i+1)*Math.PI/3)} Z`" :fill="p.c[0]" :opacity="0.25+ (i%3)*0.18" />
              </g>
            </template>
            <template v-else-if="p.type === 'bubble'">
              <circle v-for="(b, i) in [[40,76,10],[78,48,20],[116,70,14],[150,38,26]]" :key="i" :cx="b[0]" :cy="b[1]" :r="b[2]" :fill="p.c[0]" :opacity="0.35+ i*0.15" />
            </template>
            <template v-else-if="p.type === 'timeline'">
              <line x1="20" y1="84" x2="180" y2="84" :stroke="p.c[1]" opacity="0.4" />
              <circle v-for="(d, i) in [36,64,92,120,148]" :key="i" :cx="d" :cy="66+ (i%3)*14" r="5" :fill="p.c[0]" />
            </template>
            <template v-else>
              <rect x="28" y="28" width="144" height="64" rx="12" fill="none" :stroke="p.c[1]" opacity="0.35" />
              <circle v-for="(d, i) in [[54,46],[88,68],[126,44],[158,72]]" :key="i" :cx="d[0]" :cy="d[1]" r="5" :fill="p.c[0]" />
            </template>
          </svg>
        </div>
        <div class="meta">
          <div class="tags"><span v-for="t in p.tags" :key="t">{{ t }}</span></div>
          <h2>{{ p.zh }}</h2>
          <p class="en">{{ p.en }}</p>
          <p class="desc">{{ p.desc }}</p>
          <span class="go">进入作品 →</span>
        </div>
      </a>
    </section>

    <footer class="foot">
      <p>作品集入口 · 每个作品均可独立运行，链接指向各自 GitHub Pages 站点。</p>
    </footer>
  </main>
</template>

<style>
:root { --bg: #f6f4ef; --ink: #17140f; --muted: #7d7568; --line: #e2ddd1; color-scheme: light; }
* { box-sizing: border-box; }
body { margin: 0; background: var(--bg); color: var(--ink); font-family: 'Segoe UI', system-ui, sans-serif; }
.gallery { max-width: 1180px; margin: 0 auto; padding: 56px 24px 80px; }
.kicker { color: #b4533a; letter-spacing: 0.34em; text-transform: uppercase; font-size: 12px; }
h1 { font-size: clamp(48px, 9vw, 104px); line-height: 0.96; margin: 14px 0 18px; letter-spacing: -0.02em; }
.lede { max-width: 58ch; font-size: 17px; line-height: 1.7; color: #4c463a; }

.filters { display: flex; flex-wrap: wrap; gap: 8px; margin: 30px 0 26px; }
.filters button { background: transparent; border: 1px solid var(--line); color: var(--ink); border-radius: 999px; padding: 8px 16px; cursor: pointer; font-size: 14px; }
.filters button.on { background: var(--ink); color: var(--bg); border-color: var(--ink); }
.filters button:focus-visible { outline: 2px solid #b4533a; outline-offset: 2px; }

.grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 20px; }
.card { display: block; text-decoration: none; color: inherit; background: #fffdf8; border: 1px solid var(--line); border-radius: 18px; overflow: hidden; transition: transform 0.18s, box-shadow 0.18s; }
.card:hover { transform: translateY(-4px); box-shadow: 0 16px 34px rgba(40,30,15,0.10); }
.cover { height: 150px; }
.cover-svg { width: 100%; height: 100%; }
.meta { padding: 18px 20px 20px; }
.tags { display: flex; gap: 6px; margin-bottom: 10px; }
.tags span { font-size: 11px; color: #7d7568; border: 1px solid var(--line); border-radius: 999px; padding: 3px 9px; }
.meta h2 { margin: 0; font-size: 22px; }
.en { margin: 2px 0 8px; color: #9a9183; font-size: 13px; letter-spacing: 0.04em; }
.desc { margin: 0 0 14px; color: #4c463a; font-size: 14px; line-height: 1.6; }
.go { color: #b4533a; font-weight: 600; font-size: 14px; }

.foot { margin-top: 48px; border-top: 1px solid var(--line); padding-top: 22px; color: var(--muted); font-size: 13px; }
@media (prefers-reduced-motion: reduce) { * { transition: none !important; } }
</style>
