---
theme: default
colorSchema: light
layout: cover
fonts:
  sans: Noto Sans TC
  mono: Fira Code
  provider: google
  weights: '300,400'
duration: 10min
timer: countdown
aspectRatio: 16/9
canvasWidth: 980
defaults:
  layout: bilingual
drawings:
  enabled: false
transition: fade
title: "Goroutines to Fibers: A Rails Developer's Survival Guide"
author: Mu-Fan Teng (ryudo)
shiki:
  theme: github-dark
---

<div style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:1.5rem;">
  <h1 style="font-size:2.2rem;font-weight:300;color:#464462;line-height:1.4;max-width:800px;text-align:center;">
    Goroutines to Fibers:<br>A Rails Developer's Survival Guide
  </h1>
  <p style="font-size:1.1rem;font-weight:300;color:#7B7B7B;text-align:center;">
    從 Goroutine 到 Fiber：Rails 開發者的生存指南
  </p>
  <p style="font-size:0.9rem;font-weight:300;color:#7B7B7B;margin-top:1rem;text-align:center;">
    Mu-Fan Teng / <span style="color:#F43F5E;">@ryudoawaru</span> · RubyJam 2026
  </p>
</div>

<!--
I rewrote a Go server in Ruby.
It looked simple. It wasn't.
Here's what I hit.
-->

---

I built a production WebSocket proxy<br>in pure Ruby. No Rails. No Puma.

::zh::

用純 Ruby 寫了一個 production WebSocket proxy。沒有 Rails，沒有 Puma。

<!--
The project: a WebSocket proxy for remote desktop.
Written in Go. I rewrote it in Ruby.
Two reasons: integrate with Rails, and prove Async can handle real infrastructure.
-->

---
layout: default
---

<div class="arch-slide">
  <div class="arch-row">
    <div class="abox">User<br><small>Browser</small></div>
    <div class="aseg">
      <span class="aseg-fwd">Guacamole Protocol →</span>
      <div class="aseg-line"></div>
      <span class="aseg-bwd">← Guacamole Protocol</span>
      <span class="aseg-sub">WebSocket</span>
    </div>
    <div class="abox abox-accent">Lens<br><small>Pure Ruby</small></div>
    <div class="aseg">
      <span class="aseg-fwd">Guacamole Protocol →</span>
      <div class="aseg-line"></div>
      <span class="aseg-bwd">← Guacamole Protocol</span>
      <span class="aseg-sub">TCP</span>
    </div>
    <div class="abox">Apache<br><small>Guacd</small></div>
    <div class="aseg">
      <span class="aseg-fwd">RDP / VNC / SSH →</span>
      <div class="aseg-line"></div>
      <span class="aseg-bwd">← image / text</span>
      <span class="aseg-sub">native protocol</span>
    </div>
    <div class="abox-backends">
      <span>RDP</span><span>SSH</span><span>VNC</span><span>Telnet</span>
    </div>
  </div>
  <p class="arch-caption">Architecture — 用 Ruby 橋接 Browser 和 Guacd</p>
</div>

<style>
.arch-slide { display:flex; flex-direction:column; align-items:center; justify-content:center; height:100%; background:#fff; padding:2rem 2.5rem; }
.arch-row { display:flex; align-items:center; gap:0; }
.abox { padding:0.65rem 1rem; border:2px solid #464462; border-radius:8px; font-size:0.9rem; font-weight:400; color:#464462; text-align:center; line-height:1.4; white-space:nowrap; flex-shrink:0; }
.abox small { font-size:0.65rem; font-weight:300; color:#7B7B7B; display:block; }
.abox-accent { border-color:#F43F5E; color:#F43F5E; }
.abox-accent small { color:#F43F5E; opacity:0.7; }
.aseg { display:flex; flex-direction:column; align-items:center; gap:0.1rem; flex:1; min-width:120px; padding:0 0.2rem; }
.aseg-fwd { font-size:0.58rem; color:#464462; font-weight:400; white-space:nowrap; }
.aseg-bwd { font-size:0.58rem; color:#F43F5E; font-weight:400; white-space:nowrap; }
.aseg-line { width:100%; height:1px; background:#c8c8d8; }
.aseg-sub { font-size:0.5rem; color:#aaaabc; font-weight:300; margin-top:0.1rem; }
.abox-backends { display:flex; flex-direction:column; gap:0.2rem; flex-shrink:0; }
.abox-backends span { padding:0.2rem 0.65rem; border:1px solid #c0c0cc; border-radius:4px; font-size:0.72rem; color:#7B7B7B; text-align:center; }
.arch-caption { margin-top:2.5rem; font-size:1.1rem; font-weight:300; color:#7B7B7B; }
</style>

<!--
Browser connects to Lens over WebSocket.
Lens connects to Apache Guacd over TCP.
Guacd speaks RDP, VNC, SSH to the actual machines.
Lens is the Ruby proxy in the middle.
-->

---
layout: default
---

<div class="gp-slide">
  <p class="gp-title">Guacamole Protocol</p>
  <div class="gp-track-wrap">
    <div class="gp-node">Client<br><small>Browser</small></div>
    <div class="gp-track">
      <div class="gp-lane">
        <div class="gp-lane-line"></div>
        <div class="gp-packet gp-pkt-fwd">5.mouse,1.0,3.320,3.240,1.1;</div>
      </div>
      <div class="gp-lane">
        <div class="gp-lane-line"></div>
        <div class="gp-packet gp-pkt-bwd">3.img,1.1,2.12,1.0,9.image/png,…</div>
      </div>
    </div>
    <div class="gp-node">Guacd<br><small>Server</small></div>
  </div>
  <div class="gp-legend">
    <span class="gp-leg-fwd">→ client instruction (mouse / key / clipboard)</span>
    <span class="gp-leg-bwd">← server response (img / audio / sync)</span>
  </div>
  <p class="gp-caption">文字協議，雙向傳輸，橋接 WebSocket 和 TCP</p>
  <a class="gp-ref" href="https://guacamole.apache.org/doc/gug/guacamole-protocol.html" target="_blank">guacamole.apache.org · Guacamole Protocol</a>
</div>

<style>
.gp-slide { display:flex; flex-direction:column; align-items:center; justify-content:center; height:100%; background:#fff; padding:1.5rem 3rem; gap:1.2rem; }
.gp-title { font-size:1.8rem; font-weight:300; color:#464462; margin:0; }
.gp-track-wrap { display:flex; align-items:center; width:100%; max-width:680px; gap:0; }
.gp-node { padding:0.6rem 1.1rem; border:2px solid #464462; border-radius:8px; font-weight:400; color:#464462; font-size:0.9rem; text-align:center; white-space:nowrap; flex-shrink:0; line-height:1.4; }
.gp-node small { font-size:0.6rem; color:#7B7B7B; display:block; font-weight:300; }
.gp-track { flex:1; display:flex; flex-direction:column; gap:0.6rem; padding:0 0.5rem; }
.gp-lane { position:relative; height:28px; display:flex; align-items:center; overflow:hidden; }
.gp-lane-line { position:absolute; inset:50% 0 auto 0; height:1px; background:#e0e0ea; transform:translateY(-50%); }
.gp-packet { position:absolute; top:50%; transform:translateY(-50%); background:#1e1c2e; color:#e0def4; font-family:'Fira Code',monospace; font-size:0.65rem; padding:0.2rem 0.55rem; border-radius:4px; white-space:nowrap; }
.gp-pkt-fwd { animation:gp-fwd 5s ease-in-out infinite; left:-60%; }
.gp-pkt-bwd { animation:gp-bwd 5s ease-in-out 2.5s infinite; left:110%; border-left:2px solid #F43F5E; }
@keyframes gp-fwd {
  0%   { left:-60%; opacity:0; }
  8%   { opacity:1; }
  85%  { opacity:1; left:110%; }
  86%  { opacity:0; left:110%; }
  100% { opacity:0; left:-60%; }
}
@keyframes gp-bwd {
  0%   { left:110%; opacity:0; }
  8%   { opacity:1; }
  85%  { opacity:1; left:-60%; }
  86%  { opacity:0; left:-60%; }
  100% { opacity:0; left:110%; }
}
.gp-legend { display:flex; flex-direction:column; gap:0.3rem; font-size:0.75rem; font-weight:300; align-self:flex-start; padding-left:calc((100% - 680px) / 2 + 0px); }
.gp-leg-fwd { color:#464462; }
.gp-leg-bwd { color:#F43F5E; }
.gp-caption { font-size:1rem; font-weight:300; color:#7B7B7B; margin:0; }
.gp-ref { font-size:0.7rem; font-weight:300; color:#aaaabc; text-decoration:none; font-style:italic; }
</style>

<!--
Plain-text, length-prefixed format.
Top lane: client sends mouse input.
Bottom lane: server sends back image data.
Same wire format over WebSocket and TCP.
-->

---
layout: default
---

<div style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:2rem;padding:2rem 4rem;">
  <p style="font-size:1.6rem;font-weight:300;color:#464462;margin:0;">Go → Ruby: the mapping looks <span style="color:#F43F5E;font-weight:400;">1-to-1</span></p>
  <table style="width:100%;max-width:700px;border-collapse:collapse;font-size:1rem;font-weight:300;">
    <thead>
      <tr style="border-bottom:2px solid #e8e8ee;">
        <th style="text-align:left;padding:0.6rem 1rem;color:#7B7B7B;font-weight:400;">Go</th>
        <th style="text-align:center;padding:0.6rem 0.5rem;color:#7B7B7B;font-weight:400;">→</th>
        <th style="text-align:left;padding:0.6rem 1rem;color:#7B7B7B;font-weight:400;">Ruby Async</th>
        <th style="text-align:left;padding:0.6rem 1rem;color:#7B7B7B;font-weight:400;">Purpose</th>
      </tr>
    </thead>
    <tbody>
      <tr style="border-bottom:1px solid #f0f0f5;">
        <td style="padding:0.6rem 1rem;"><code style="color:#464462;font-family:'Fira Code',monospace;">go &lt;func literal&gt;</code></td>
        <td style="text-align:center;padding:0.6rem 0.5rem;color:#7B7B7B;">→</td>
        <td style="padding:0.6rem 1rem;"><code style="color:#F43F5E;font-family:'Fira Code',monospace;">barrier.async {}</code></td>
        <td style="padding:0.6rem 1rem;color:#7B7B7B;font-size:0.85rem;">spawn task</td>
      </tr>
      <tr style="border-bottom:1px solid #f0f0f5;">
        <td style="padding:0.6rem 1rem;"><code style="color:#464462;font-family:'Fira Code',monospace;">chan struct{}</code></td>
        <td style="text-align:center;padding:0.6rem 0.5rem;color:#7B7B7B;">→</td>
        <td style="padding:0.6rem 1rem;"><code style="color:#F43F5E;font-family:'Fira Code',monospace;">Async::Condition</code></td>
        <td style="padding:0.6rem 1rem;color:#7B7B7B;font-size:0.85rem;">signal "done" between tasks</td>
      </tr>
      <tr style="border-bottom:1px solid #f0f0f5;">
        <td style="padding:0.6rem 1rem;"><code style="color:#464462;font-family:'Fira Code',monospace;">sync.WaitGroup</code></td>
        <td style="text-align:center;padding:0.6rem 0.5rem;color:#7B7B7B;">→</td>
        <td style="padding:0.6rem 1rem;"><code style="color:#F43F5E;font-family:'Fira Code',monospace;">Async::Barrier</code></td>
        <td style="padding:0.6rem 1rem;color:#7B7B7B;font-size:0.85rem;">group + stop tasks</td>
      </tr>
      <tr>
        <td style="padding:0.6rem 1rem;"><code style="color:#464462;font-family:'Fira Code',monospace;">goroutine</code></td>
        <td style="text-align:center;padding:0.6rem 0.5rem;color:#7B7B7B;">→</td>
        <td style="padding:0.6rem 1rem;"><code style="color:#F43F5E;font-family:'Fira Code',monospace;">Fiber</code></td>
        <td style="padding:0.6rem 1rem;color:#7B7B7B;font-size:0.85rem;">concurrency unit</td>
      </tr>
    </tbody>
  </table>
  <p style="font-size:1.1rem;font-weight:300;color:#7B7B7B;margin:0;">看起來很簡單，對吧？</p>
</div>

<!--
The primitives map cleanly.
In Go, "go" plus a func literal launches a goroutine — a lightweight concurrent unit.
barrier.async does the same for Ruby fibers.
chan struct{} is Go's idiom for "signal only, no value" — like a doorbell, not a pipe.
Async::Condition plays the same role: one task signals, another waits.
On paper, a straightforward port.
-->

---

It wasn't.

::zh::

才沒有。

<!--
It wasn't.
I hit three traps.
Each one came from a different assumption I was carrying over.
-->

---

<span class="accent">Trap 1</span><br>The Ecosystem

::zh::

生態系

<!--
Trap one: the ecosystem.
-->

---

Go has a stable stdlib.<br>Ruby's async ecosystem is still moving.

::zh::

Go 的標準庫很穩定。Ruby 的 async 生態系還在快速演進。

<!--
Go's standard library barely breaks across versions.
In Ruby, async-http, io-stream, protocol-http — all moved fast.
No Rails to absorb the impact.
-->

---
layout: default
---

<div class="inv-slide">
  <div class="inv-title">Bug 1 — <code>async-http</code></div>
  <div class="inv-steps">
    <div class="inv-step inv-symptom">
      <span class="inv-label">Symptom</span>
      WebSocket handshake fails silently after gem update
    </div>
    <div class="inv-step inv-debug">
      <span class="inv-label">Debug</span>
      <code>request.headers['upgrade']</code> → <code>nil</code>, always
    </div>
    <div class="inv-step inv-search">
      <span class="inv-label">Searched</span>
      <a class="inv-link">github.com/socketry/async-http · releases.md</a>
      — no breaking change notice
    </div>
    <div class="inv-step inv-found">
      <span class="inv-label">Root cause</span>
      Upgrade header moved to <code>request.protocol</code> in v0.92
    </div>
    <div class="inv-step inv-fix">
      <span class="inv-label">Fix</span>
      <code>Async::WebSocket::Adapters::HTTP.open(request) { |ws| … }</code>
    </div>
  </div>
  <p class="inv-caption">沒有 PR，沒有 migration note。只有一個 nil。</p>
</div>


<!--
ref: https://github.com/socketry/async-http/blob/main/releases.md

I upgraded async-http. WebSocket stopped working. No error — just nil.
Dug into the source. Found the Upgrade header was now in request.protocol.
No PR. No warning. No migration guide.
-->

---
layout: default
---

<div class="inv-slide">
  <div class="inv-title">Bug 2 — <code>io-stream</code></div>
  <div class="inv-steps">
    <div class="inv-step inv-symptom">
      <span class="inv-label">Symptom</span>
      <code>NameError: uninitialized constant IO::Stream</code>
    </div>
    <div class="inv-step inv-debug">
      <span class="inv-label">Debug</span>
      <code>IO::Stream.new(socket)</code> — used to work, now crashes
    </div>
    <div class="inv-step inv-search">
      <span class="inv-label">Searched</span>
      <a class="inv-link">github.com/socketry/io-stream · releases.md</a>
      — found v0.4.0: "Add convenient <code>IO.Stream()</code> constructor"
    </div>
    <div class="inv-step inv-found">
      <span class="inv-label">Root cause</span>
      <code>IO::Stream</code> became a module. <code>.new</code> no longer valid.
    </div>
    <div class="inv-step inv-fix">
      <span class="inv-label">Fix</span>
      <code>IO.Stream(socket)</code>
    </div>
  </div>
  <p class="inv-caption">v0.4.0 的 release note 只說「新增 constructor」，沒說舊的壞了。</p>
</div>

<!--
ref: https://github.com/socketry/io-stream/blob/main/releases.md

IO::Stream.new stopped working after a minor version bump.
The release note only said "add convenient IO.Stream() constructor."
It didn't say IO::Stream.new was broken. No dedicated PR. No migration note.
-->

---

Two bugs. Two gem updates.<br>Zero breaking change notices.

::zh::

兩個 bug，兩次版本更新，零個 breaking change 警告。

<!--
Neither had a dedicated PR.
Neither had a migration guide.
Both were caught only by running the code.
This is what "you own every changelog" means.
-->

---

No Rails to pin your deps.<br>You own every changelog.

::zh::

沒有 Rails 幫你鎖版本。每個 gem 的 changelog，你得自己追。

<!--
In Rails, the framework pins compatible versions for you.
Step outside it, and you own every breaking change.
Lock your Gemfile. Read changelogs. No shortcuts.
-->

---

<span class="accent">Trap 2</span><br>The Runtime

::zh::

執行環境

<!--
Trap two: the runtime.
-->

---

Go's `go <func literal>` is explicitly a spawn.<br>Ruby's `Async {}` looks like a block.

::zh::

Go 的 `go <func literal>` 明確是一個 spawn。Ruby 的 `Async {}` 看起來像一個 block。

<!--
In Go, "go" plus a func literal is visually distinct — you know it spawns a goroutine.
In Ruby, Async {} looks like any other block. It isn't.
It spawns a fiber task and returns immediately.
-->

---
layout: bilingual-code
---

```ruby
def run
  Async { handle_connection }
ensure
  update_state('closed')  # fires immediately — connection still alive
end

# Fix: run inside existing task context
def run
  barrier = Async::Barrier.new
  done = Async::Condition.new

  barrier.async { handle_connection; done.signal }
  done.wait
ensure
  barrier&.stop
  update_state('closed')  # now fires after connection ends
end
```

::zh::

`ensure` 不等 task 結束。需要用 Condition 明確等待。

<!--
Async {} returns right away.
So ensure runs while the connection is still alive.
The fix: use Condition to wait for the task to signal completion.
Then ensure runs at the right time.
-->

---

`Async {}` is spawn, not yield.<br>Your Rails instincts will mislead you.

::zh::

`Async {}` 是 spawn，不是 yield。Rails 的直覺在這裡會誤導你。

<!--
This is the core mental model shift.
Every block in Rails yields and returns.
Async {} spawns and returns. Completely different.
-->

---

<span class="accent">Trap 3</span><br>The OS

::zh::

作業系統

<!--
Trap three: the OS.
-->

---

Go handles signals in a goroutine.<br>Ruby's `trap` runs in an OS thread.

::zh::

Go 在 goroutine 裡處理 signal。Ruby 的 `trap` 在 OS thread 裡執行。

<!--
In Go, you handle signals inside a goroutine — just another concurrent unit.
In Ruby, trap() runs in a raw OS thread, completely outside the fiber scheduler.
-->

---
layout: bilingual-code
---

```ruby
# This crashes in production — ThreadError
trap('SIGTERM') { task.stop }

# Fix: bridge with IO.pipe
reader, writer = IO.pipe
trap('SIGTERM') { writer.write_nonblock('.') rescue nil }

Async do |task|
  task.async { reader.wait_readable; task.stop }
  server.run
end
```

::zh::

用 IO.pipe 橋接兩個世界：OS thread 和 fiber scheduler。

<!--
Calling task.stop from a signal handler hits a Mutex — ThreadError.
The fix: IO.pipe as a bridge.
Signal writes a byte. A fiber reads it and stops the scheduler.
Two worlds, safely connected.
-->

---

Signal handlers live outside<br>the fiber scheduler.

::zh::

Signal handler 活在 fiber scheduler 的外面。你不能直接碰 fiber world 的物件。

<!--
This isn't a bug you can find in documentation.
It only crashes in production because the signal path is different there.
Puma absorbs SIGTERM for you in Rails — you never see this.
-->

---
layout: default
---

<div style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:1.5rem;padding:2rem 4rem;">
  <p style="font-size:1.4rem;font-weight:300;color:#464462;margin:0;text-align:center;">One root cause behind all three traps</p>
  <div style="display:flex;gap:3rem;align-items:flex-start;margin-top:0.5rem;">
    <div style="text-align:center;flex:1;">
      <p style="font-size:1rem;font-weight:400;color:#464462;margin:0 0 0.5rem;">Go</p>
      <p style="font-size:0.85rem;font-weight:300;color:#7B7B7B;line-height:1.6;margin:0;">Preemptive scheduling<br>Goroutines are OS threads<br>Runtime manages switching<br>Signal = just another goroutine</p>
    </div>
    <div style="text-align:center;flex:0 0 auto;padding-top:2rem;color:#c8c8d8;font-size:1.5rem;">vs</div>
    <div style="text-align:center;flex:1;">
      <p style="font-size:1rem;font-weight:400;color:#F43F5E;margin:0 0 0.5rem;">Ruby Async</p>
      <p style="font-size:0.85rem;font-weight:300;color:#7B7B7B;line-height:1.6;margin:0;">Cooperative scheduling<br>Fibers yield at I/O points<br>One OS thread<br>Signal = different world</p>
    </div>
  </div>
  <p style="font-size:0.9rem;font-weight:300;color:#7B7B7B;margin-top:1rem;text-align:center;">
    Ecosystem moves fast → no framework to catch breaking changes<br>
    Async {} spawns → ensure fires too early<br>
    trap → touches fiber world from OS thread
  </p>
</div>

<!--
All three traps share one root: cooperative scheduling.
Go is preemptive — goroutines are scheduled by the runtime.
Ruby Async is cooperative — fibers yield at I/O. One OS thread.
Once you internalize this, all three traps make sense.
-->

---
layout: default
---

<div style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:1.2rem;padding:2rem 4rem;">
  <p style="font-size:1.6rem;font-weight:300;color:#464462;margin:0;">Three traps. One mindset shift.</p>
  <div style="display:flex;flex-direction:column;gap:1rem;width:100%;max-width:700px;margin-top:0.5rem;">
    <div style="display:flex;align-items:center;gap:1.5rem;">
      <span style="color:#F43F5E;font-weight:400;min-width:130px;">Ecosystem</span>
      <span style="color:#7B7B7B;">→</span>
      <span style="font-weight:300;color:#464462;">Lock deps. No framework safety net. <span style="color:#7B7B7B;font-size:0.85rem;">/ 鎖版本，沒有框架保護</span></span>
    </div>
    <div style="display:flex;align-items:center;gap:1.5rem;">
      <span style="color:#F43F5E;font-weight:400;min-width:130px;">Runtime</span>
      <span style="color:#7B7B7B;">→</span>
      <span style="font-weight:300;color:#464462;">Async {} is spawn. Wait explicitly. <span style="color:#7B7B7B;font-size:0.85rem;">/ 明確等待，不要假設 block</span></span>
    </div>
    <div style="display:flex;align-items:center;gap:1.5rem;">
      <span style="color:#F43F5E;font-weight:400;min-width:130px;">OS</span>
      <span style="color:#7B7B7B;">→</span>
      <span style="font-weight:300;color:#464462;">Bridge OS threads and fibers. <span style="color:#7B7B7B;font-size:0.85rem;">/ 橋接兩個世界</span></span>
    </div>
  </div>
</div>

<!--
Ecosystem: lock your gems, own your changelogs.
Runtime: Async is spawn, not yield — wait explicitly.
OS: bridge OS threads and fibers with IO.pipe.
-->

---
layout: default
---

<div class="sosi-slide">
  <div class="sosi-left">
    <span class="sosi-brand">SOSI</span>
    <span class="sosi-tagline">Remote desktop<br>in the browser.</span>
    <span class="sosi-sub">RDP / VNC / SSH — streamed to the browser<br>over WebSocket. No client software needed.</span>
    <p class="sosi-zh">瀏覽器直接連遠端桌面。不需要安裝任何軟體。</p>
    <a class="sosi-url">sosi.com.tw</a>
  </div>
  <div class="sosi-right">
    <video src="/sosi-demo.webm" autoplay loop muted playsinline preload="auto"
           onloadeddata="this.playbackRate=2;this.play()" class="sosi-video"></video>
  </div>
</div>

<style>
.sosi-slide { display:flex; align-items:center; height:100%; background:#fff; padding:2rem 3.5rem; gap:2.5rem; }
.sosi-left { flex:0 0 36%; display:flex; flex-direction:column; gap:0.7rem; }
.sosi-brand { font-size:3rem; font-weight:400; color:#F43F5E; line-height:1; letter-spacing:0.02em; }
.sosi-tagline { font-size:1.45rem; font-weight:300; color:#464462; line-height:1.4; }
.sosi-sub { font-size:0.78rem; font-weight:300; color:#7B7B7B; line-height:1.6; border-top:1px solid #e8e8ee; padding-top:0.7rem; margin-top:0.2rem; }
.sosi-zh { font-size:0.95rem; font-weight:300; color:#7B7B7B; margin:0; border-top:1px solid #e8e8ee; padding-top:0.7rem; }
.sosi-url { font-size:0.85rem; font-weight:400; color:#F43F5E; text-decoration:none; }
.sosi-right { flex:1; display:flex; align-items:center; justify-content:center; }
.sosi-video { width:100%; max-height:400px; border-radius:10px; box-shadow:0 4px 28px rgba(70,68,98,0.15); object-fit:cover; display:block; }
</style>

<script setup>
import { onMounted, nextTick } from 'vue'
onMounted(() => {
  nextTick(() => {
    const v = document.querySelector('.sosi-video')
    if (v) { v.load(); v.playbackRate = 2; v.play().catch(() => {}) }
  })
})
</script>

<!--
This is SOSI — the product running this in production.
sosi.com.tw if you're curious.
-->

---
layout: center
---

<div style="display:flex;flex-direction:column;align-items:center;justify-content:center;height:100%;gap:1.5rem;text-align:center;">
  <p style="font-size:2rem;font-weight:300;color:#464462;line-height:1.5;max-width:740px;">
    Ruby Async is production-ready.<br>Unlearn first, then build.
  </p>
  <p style="font-size:1.1rem;font-weight:300;color:#7B7B7B;">
    Ruby Async 已經可以跑 production 了。先卸下舊的假設，再開始建。
  </p>
  <p style="font-size:0.9rem;font-weight:300;color:#7B7B7B;margin-top:1.5rem;">
    <span style="color:#F43F5E;">@ryudoawaru</span> · Ruby Taiwan
  </p>
</div>

<!--
Ruby Async works for real infrastructure.
But you need to unlearn Rails habits and Go assumptions first.
Thank you.
-->
