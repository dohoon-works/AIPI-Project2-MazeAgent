# Project 2 Presentation Script v2
### Slide-by-slide alignment with notebook code cuts

**Recording setup recommendation**
- Main screen: full-screen slides (PowerPoint presentation mode)
- Secondary window ready: Colab notebook, cells pre-executed, already scrolled to the relevant Part
- When script says **[→ NOTEBOOK]**, cut / alt-tab to the notebook; **[→ SLIDE]** returns
- Notebook cuts should stay **10–20 seconds max** — the audience needs to see the code exists and looks the way you described, not read every line

**Pause markers**: `/` = short (breath), `//` = longer (emphasis beat)

**Total target: ~10:00**

---

## 🎬 Timing Budget

| Slide | Duration | Cumulative | Code cut? |
|-------|----------|------------|-----------|
| 1. Title | 0:15 | 0:15 | — |
| 2. The question | 0:50 | 1:05 | — |
| 3. Task | 0:40 | 1:45 | — |
| 4. Non-DL agent | 1:00 | 2:45 | ✔ Part 2.1 + 2.2 |
| 5. DL agent | 1:20 | 4:05 | ✔ Part 3.1 + 3.2 |
| 6. Why not alternatives | 0:40 | 4:45 | — |
| 7. Demo 1 — Clean | 0:35 | 5:20 | ✔ Part 5 Demo 1 output |
| 8. Demo 2 — Rotated | 0:35 | 5:55 | ✔ Part 5 Demo 2 output |
| 9. Demo 3 — Cascade | 0:55 | 6:50 | ✔ Part 5 Demo 3 + visualization |
| 10. Evaluation chart | 0:55 | 7:45 | ✔ Part 4.2 summary tables |
| 11. Cascade math | 0:50 | 8:35 | — |
| 12. Lessons + closing | 1:25 | 10:00 | — |

Budget check: ~10 minutes total, with ~2 minutes of notebook cuts distributed across 6 moments.

---

# 🇰🇷 Korean Version

---

## Slide 1 — Title · 0:15

*(Let the slide just display. No voice-over needed for the first 5 seconds. Then:)*

**스크립트**  
Visual Maze-Solving Agent. / Two architectures, / two failure modes. // Project 2 시작합니다. //

---

## Slide 2 — The question · 0:50

**스크립트**  
같은 에이전트를 두 가지 방식으로 만들면 / 어떤 일이 벌어질까요? //

이 프로젝트는 / 비전 기반 미로 풀이라는 하나의 과제를 잡고 / 완성된 에이전트 두 개를 만들었습니다. // 첫 번째는 OpenCV와 BFS — / 완전히 classical. // 두 번째는 Claude의 vision 모델과 / BFS trajectory로 학습시킨 신경망 — / 완전히 deep learning 기반. //

가설은 단순했습니다. / Classical은 brittle, / deep learning은 robust할 것이다. // 실제 결과는 / 훨씬 더 흥미로웠습니다. // 두 시스템 모두 brittle한데, / 완전히 다른 방식으로 실패합니다. // 그 차이가 / 이 발표의 핵심입니다. //

---

## Slide 3 — Task · 0:40

**스크립트**  
과제는 단순합니다. // 10×10 미로 이미지가 입력으로 들어오고 / 출발점에서 목표 지점까지 가는 / 행동 시퀀스를 출력해야 합니다. //

에이전트는 세 단계로 구성됩니다. / Perception은 픽셀에서 grid 구조를 추출하고, / Planning은 start에서 goal로 가는 경로를 찾고, / Control은 그 계획을 실행하며 / 합법성을 검증합니다. //

초록 원이 출발점, / 빨간 원이 목표점. / 검은 셀은 벽, / 흰 셀은 길입니다. //

---

## Slide 4 — Non-DL agent · 1:00

**스크립트**  
Non-DL 에이전트의 파이프라인입니다. //

Perception은 의도적으로 최소한입니다.

**[→ NOTEBOOK: Part 2.1 — perceive_classical 함수 셀로 이동]**

스크립트를 보시면 / 각 grid cell에 대해 중심 픽셀 하나를 샘플링하고 / 색상으로 threshold할 뿐입니다. / 검정은 벽, / 흰색은 길, / 초록은 start, / 빨강은 goal. / 전체 perception 코드가 / 약 20줄입니다. //

**[→ NOTEBOOK: Part 2.2 — plan_bfs 함수 셀로 스크롤]**

그리고 planning은 / Assignment 1에서 구축한 BFS planner를 / 그대로 재사용합니다. / 수정 없이. / Grid navigation이 / 이전 state-action formulation에 깔끔하게 매핑되기 때문입니다. //

**[→ SLIDE 4]**

이 단순함은 의도적입니다. / Classical이 heuristic 없이 / 실제로 무엇을 제공하는지 / 분리해서 보고 싶었기 때문입니다. / Fast, / deterministic, / fully interpretable — / 이것이 baseline입니다. //

---

## Slide 5 — DL agent · 1:20

**스크립트**  
DL 에이전트는 두 가지 결정이 핵심입니다. //

첫째, / perception. / Custom CNN을 학습시키는 대신 / Claude의 vision 모델에 위임했습니다.

**[→ NOTEBOOK: Part 3.1 — perceive_dl 함수 셀로 이동. 프롬프트 부분이 보이도록]**

프롬프트는 간단합니다. / "이것은 10×10 미로 이미지다, / 검정은 벽, / 흰색은 길, / JSON으로 grid를 돌려달라." / 모델이 zero-shot으로 해결합니다. / 학습 데이터도, / GPU 시간도 / 필요 없습니다. //

**[→ NOTEBOOK: Part 3.2 — PolicyNet 클래스 셀로 스크롤]**

둘째, / planning은 / Behavior Cloning입니다. / 약 3000개의 (state, optimal_action) 쌍을 / 랜덤 미로들에 대한 BFS solution에서 추출한 다음 / 작은 MLP를 학습시켰습니다. / 순수 supervised learning으로 환원되기 때문에 / reward shaping도 replay buffer도 없습니다. //

**[→ SLIDE 5]**

Trade-off은 명확합니다. / 추론이 천 배 느려지고, / API 호출은 non-deterministic하며, / BC policy는 / 학습 중에 본 상태만 알 수 있습니다. //

이 마지막 점이 / 잠시 뒤 중요해집니다. //

---

## Slide 6 — Why not the alternatives? · 0:40

**스크립트**  
여기서 자주 받는 두 가지 질문에 먼저 답하겠습니다. //

왜 custom CNN을 학습시키지 않았나? // CNN은 데이터셋 큐레이션, / augmentation, / 학습 시간이 필요합니다. / VLM은 zero-shot으로 풀어버립니다. / Trade-off는 학습 복잡도를 / API latency와 비용으로 교환하는 것입니다. //

왜 DQN이 아니라 Behavior Cloning인가? // DQN은 reward shaping, / replay buffer, / 세심한 튜닝이 필요합니다. / BC는 supervised learning으로 환원됩니다 / — 훨씬 단순하지만 / 학습 분포를 벗어나면 일반화하지 못합니다. //

설계 목표는 / DL의 "capability"를 보여주는 것이지 / training infrastructure를 보여주는 것이 아니었습니다. //

---

## Slide 7 — Demo 1 · Clean maze · 0:35

**스크립트**  
실제로 어떻게 작동하는지 보여드리겠습니다. //

Clean maze에서는 / 두 에이전트 모두 성공합니다.

**[→ NOTEBOOK: Part 5 — Demo 1 (Clean maze) 출력 셀]**

보시다시피 / 두 에이전트 모두 같은 최적 경로를 찾습니다. / Non-DL은 약 4 millisecond에 끝나고, / DL은 API 호출 포함 약 4초 걸립니다 — / 대략 천 배 느리죠. //

**[→ SLIDE 7]**

이상적인 입력에서는 / classical이 속도로 이깁니다. //

---

## Slide 8 — Demo 2 · Rotated maze · 0:35

**스크립트**  
입력을 10도 회전시키면 / Non-DL이 완전히 무너집니다.

**[→ NOTEBOOK: Part 5 — Demo 2 (Rotated maze) 출력 셀]**

보시다시피 Non-DL 경로가 실종됐거나 / 이상하게 그려집니다. / 중심 픽셀 sampler가 / grid가 축에 정렬되어 있다고 가정하기 때문에 / 작은 회전만 있어도 / 모든 cell 샘플이 몇 픽셀씩 어긋납니다. / Perception 정확도가 80%로 떨어지고 / Success rate는 0%가 됩니다. //

**[→ SLIDE 8]**

Classical은 / binary하게 실패합니다. / 완벽하게 작동하거나, / 아예 작동하지 않거나. //

---

## Slide 9 — Demo 3 · The surprise · 0:55

**스크립트**  
이제 이 프로젝트에서 가장 흥미로운 경우입니다. / Clean maze — / noisy하지도, / rotated하지도 않은, / Non-DL이 완벽하게 푸는 종류의 입력 — / 그런데 DL이 실패합니다.

**[→ NOTEBOOK: Part 5 — Demo 3 출력 셀. 혹은 cascade 시각화 이미지]**

보시는 이 빨간 경로가 / DL 에이전트가 제안한 경로입니다. / 그런데 노란색 X 표시가 된 셀을 통과하죠. / 이 셀은 / 실제 미로에서는 벽입니다. // VLM이 이 셀 하나를 잘못 perception했고, / policy network는 / 그 잘못된 "지각된 grid" 위에서 / 완벽하게 최적 경로를 계산했습니다. / 실제 환경에서 실행하면 / 에이전트가 벽을 통과하게 됩니다. / Legality check이 당연히 거부하죠. //

**[→ SLIDE 9]**

이것이 cascade failure입니다. // VLM의 92% 정확도는 / 혼자 놓고 보면 높은 숫자이지만, / 입력을 완벽하다고 가정하는 planner를 구동하기에는 / 전혀 충분하지 않습니다. // 그리고 이것이 / 이 프로젝트의 가장 중요한 관찰입니다. //

---

## Slide 10 — Evaluation · 0:55

**스크립트**  
전체 평가 결과입니다.

**[→ NOTEBOOK: Part 4.2 출력 — summary 표 두 개]**

20개의 미로를 / 4가지 조건 — / clean, / noisy, / dim, / rotated — / 에 걸쳐 테스트했습니다. // Non-DL은 세 카테고리에서 완벽합니다 — / 100% success rate. / 하지만 rotation에서는 0%로 떨어집니다. // DL은 / 모든 카테고리에서 perception이 80~92% 사이로 / 항상 가깝지만 절대 완벽하지 않고, / success rate은 clean과 noisy에서 40%, / dim에서 20%, / rotated에서 0%입니다. //

**[→ SLIDE 10]**

핵심 관찰은 이것입니다. // DL의 success rate이 / perception 정확도보다 / 훨씬 낮습니다. // 왜일까요? //

---

## Slide 11 — The cascade failure · 0:50

**스크립트**  
간단한 수학적 설명이 있습니다. //

Modular pipeline에서 / 각 단계가 이전 단계의 출력을 신뢰할 때 / end-to-end 성공 확률은 / 곱셈적으로 작동합니다. //

Perception이 cell당 92% 정확하고 / 경로가 20 cell을 통과한다면 / 모든 cell이 올바를 확률은 / 0.92의 20승 — / 약 19%입니다. // 관찰된 40%는 / 이 하한선보다 실제로 더 낫습니다. / BFS가 경향적으로 / perception이 더 reliable한 내부 cell들을 / 통과하기 때문입니다. //

하지만 / 구조적 포인트는 이것입니다. / Cell당 92%라는 / "높아 보이는" 숫자가 / 입력이 정확하다고 믿는 planner에게는 / 전혀 충분하지 않다는 사실. //

---

## Slide 12 — Lessons learned · 1:25

**스크립트**  
그래서 이 프로젝트가 실제로 가르쳐주는 것은 무엇일까요? / 세 가지입니다. //

**첫째, / 두 fragility.** / Classical은 binary하게 실패합니다 — / 전부 아니면 아무것도 아닌. / Deep learning은 cascade 방식으로 실패합니다 — / 가깝지만 정확하지 않은. / 이 둘은 / 서로 다른 완화 방법을 요구합니다. //

**둘째, / 정확도는 충분한 지표가 아닙니다.** / Modular pipeline에서 / end-to-end 성공 확률은 / 곱셈적입니다. / 92% perception layer가 / deterministic planner를 구동할 때 / 효과적 성능은 / 8%가 아니라 60%까지 떨어질 수 있습니다. //

**셋째, / 현실에서 가장 좋은 아키텍처는 / 순수 classical도 순수 DL도 아닙니다. / Hybrid입니다.** / Tesla Autopilot이나 Waymo 같은 production 자율주행 시스템은 / 입력 분포가 통제되지 않기 때문에 / perception에 DL을 사용하고, / optimality 보장이 필요하기 때문에 / planning에 classical algorithm을 사용하고, / 그 사이에 광범위한 validation layer를 둡니다 / — cascade failure가 알려진 실패 방식이기 때문입니다. / 이 프로젝트는 / 바로 그 설계 선택으로 이어진 아키텍처적 압력을 / 축소판으로 재현했습니다. //

다시 설계한다면 / 세 가지를 바꿀 것입니다. / VLM의 자유 형식 JSON 출력을 / structured tool use로 교체하고, / perception과 planning 사이에 connectivity check를 추가하고, / planning을 점진적 실행으로 만들어서 / 한 단계씩 이동하고 재-perception하도록 할 것입니다. //

핵심 결론은 단순합니다. // 같은 에이전트를 두 가지 방식으로 만들면서 / 어느 버전 단독으로는 드러나지 않았을 속성 하나가 보였습니다 / — / pipeline은 pipeline으로서 실패합니다, / 고립된 component로서가 아니라. // 그것을 위해 설계하는 것이 / 실험과 시스템을 구분합니다. // 감사합니다. //

---
---

# 🇺🇸 English Version

---

## Slide 1 — Title · 0:15

*(Let the slide display silently for a few seconds, then:)*

**Script**  
Visual Maze-Solving Agent. / Two architectures, / two failure modes. // Project 2. //

---

## Slide 2 — The question · 0:50

**Script**  
When you build the same agent two ways — / what actually happens? //

This project took one task — / visual maze solving — / and built two complete agents for it. // The first uses OpenCV and BFS — / fully classical. / The second uses Claude's vision model and a neural network trained on BFS trajectories — / fully deep learning. //

The hypothesis was simple. / Classical would be brittle, / deep learning would be robust. // The reality was more interesting. // Both systems are brittle, / but they fail in completely different ways. / And that difference / is the thesis of this talk. //

---

## Slide 3 — Task · 0:40

**Script**  
The task is simple. // A 10-by-10 maze image comes in as input, / and the agent must output / an action sequence from start to goal. //

Every agent has three components. / Perception extracts grid structure from pixels, / planning finds a route from start to goal, / control executes that plan / and verifies legality. //

Green circle marks the start, / red circle marks the goal. / Black cells are walls, / white cells are paths. //

---

## Slide 4 — Non-DL agent · 1:00

**Script**  
Here's the pipeline for the non-DL agent. //

Perception is deliberately minimal.

**[→ NOTEBOOK: navigate to Part 2.1 — `perceive_classical` function]**

As you can see in the code, / for each grid cell / we sample one center pixel / and threshold by color. / Black is wall, / white is path, / green is start, / red is goal. / The entire perception module / is about 20 lines. //

**[→ NOTEBOOK: scroll to Part 2.2 — `plan_bfs` function]**

And the planner is the BFS from Assignment 1, / reused directly, / no modifications. / Grid navigation maps cleanly onto the state-action formulation already built. //

**[→ SLIDE 4]**

The minimalism is intentional. / This version isolates what classical provides / without heuristics propping it up. / Fast, / deterministic, / fully interpretable — / this is the baseline. //

---

## Slide 5 — DL agent · 1:20

**Script**  
The DL agent has two key design decisions. //

First, / perception. / Instead of training a custom CNN, / this project delegates to Claude's vision model.

**[→ NOTEBOOK: Part 3.1 — `perceive_dl` function, ensure prompt is visible]**

The prompt is simple. / "This is a 10-by-10 maze image, / black is wall, / white is path, / return the grid as JSON." / The model solves it zero-shot. / No training data, / no GPU time. //

**[→ NOTEBOOK: Part 3.2 — `PolicyNet` class]**

Second, / planning uses Behavior Cloning. / Roughly 3000 state-action pairs / extracted from BFS solutions on random mazes, / then a small MLP trained on them. / It reduces to pure supervised learning / — no reward shaping, / no replay buffer. //

**[→ SLIDE 5]**

The trade-offs are clear. / Inference is about a thousand times slower, / the API call is non-deterministic, / and the BC policy / only knows states it saw during training. //

That last point / becomes important shortly. //

---

## Slide 6 — Why not the alternatives? · 0:40

**Script**  
Let me answer two questions I often get. //

Why not train a custom CNN? // A CNN would need dataset curation, / augmentation, / training time. / A VLM solves it zero-shot. / The trade-off: / training complexity swapped for API latency and cost. //

Why Behavior Cloning instead of DQN? // DQN needs reward shaping, / replay buffer, / careful tuning. / BC reduces to supervised learning / — much simpler, / but it only generalizes to states seen during training. //

The design goal was to showcase the DL capability, / not the training infrastructure. //

---

## Slide 7 — Demo 1 · Clean maze · 0:35

**Script**  
Here's what happens in practice. //

On a clean maze, / both agents succeed.

**[→ NOTEBOOK: Part 5 — Demo 1 (Clean maze) output]**

Both agents find the same optimal path. / Non-DL takes about 4 milliseconds, / DL takes about 4 seconds including the API call — / roughly a thousand times slower. //

**[→ SLIDE 7]**

On ideal inputs, / classical wins on speed. //

---

## Slide 8 — Demo 2 · Rotated maze · 0:35

**Script**  
Rotate the input by 10 degrees / and the non-DL agent collapses completely.

**[→ NOTEBOOK: Part 5 — Demo 2 (Rotated maze) output]**

You can see the Non-DL path either missing or malformed. / The center-pixel sampler assumes the grid is axis-aligned, / so even a small rotation offsets every sample by a few pixels. / Perception drops to 80%. / Success rate drops to 0%. //

**[→ SLIDE 8]**

Classical fails binary. / Works perfectly, / or not at all. //

---

## Slide 9 — Demo 3 · The surprise · 0:55

**Script**  
Now / the most interesting case in this project. / A clean maze — / not noisy, / not rotated, / the same kind of input the non-DL agent solves perfectly — / and the DL agent still fails.

**[→ NOTEBOOK: Part 5 — Demo 3 output, or the cascade-failure visualization]**

This red line is the path the DL agent produced. / Notice it passes through this cell marked with a yellow X. / That cell is a wall in the actual maze. // The VLM misclassified this one cell, / and the policy network computed a perfectly optimal path / on that flawed "perceived grid." / When executed in the real environment, / the agent walks through a wall. / The legality check rejects it. //

**[→ SLIDE 9]**

This is cascade failure. // 92% perception sounds high in isolation, / but it is not nearly high enough / to drive a planner that trusts its input. // And this / is the most important observation of this project. //

---

## Slide 10 — Evaluation · 0:55

**Script**  
Here are the full evaluation results.

**[→ NOTEBOOK: Part 4.2 output — both summary tables]**

20 mazes, / four conditions — / clean, / noisy, / dim, / rotated. // Non-DL is perfect on three of four — / 100% success rate. / But on rotation, / 0%. // DL shows perception accuracy between 80 and 92% across all conditions — / always close, / never perfect — / and success rates of 40%, 40%, 20%, 0%. //

**[→ SLIDE 10]**

Notice the pattern. // DL's success rate / is always much lower than / its perception accuracy. // Why? //

---

## Slide 11 — The cascade failure · 0:50

**Script**  
A simple piece of math explains it. //

In a modular pipeline / where each stage trusts the previous, / end-to-end success probability is / multiplicative across stages. //

If perception is 92% per cell / and a plan traverses 20 cells, / the probability of every cell being correct is / 0.92 to the 20th power — / about 19%. // Observed 40% is higher than this floor, / because BFS tends to route through interior cells / where perception happens to be more reliable. //

But the structural point is this. / 92% — / a number that sounds high — / is not nearly high enough / for a planner that assumes its input is correct. //

---

## Slide 12 — Lessons learned · 1:25

**Script**  
So what does this project actually teach? / Three things. //

**First, / two fragilities.** / Classical fails in a binary way — / all or nothing. / Deep learning fails in a cascading way — / close but not quite. / These require different mitigations. //

**Second, / accuracy is not enough.** / In modular pipelines, / end-to-end success is multiplicative. / A 92% perception layer driving a deterministic planner / has effective performance not 8% worse / but up to 60% worse than 100%. //

**Third, / the best real-world architecture is neither pure classical nor pure DL. / It's hybrid.** / Production autonomous systems like Tesla Autopilot or Waymo / use DL for perception / because the input distribution is uncontrolled, / classical algorithms for planning / because planning needs optimality guarantees, / and extensive validation layers between them / because cascade failure is a known failure mode. / This project reproduced, / in miniature, / the exact architectural pressure / that led to those design choices. //

If I were to redesign this system, / I would change three things. / Replace the VLM's free-form JSON output with structured tool use, / add a connectivity check between perception and planning, / and make planning incremental — / move one step, / re-perceive, / replan. //

The core takeaway is simple. // Building the same agent two ways made one property visible / that neither version would have shown alone / — / pipelines fail as pipelines, / not as isolated components. // And designing for that / is what separates an experiment from a system. // Thank you. //

---

# 📹 Recording Notes

## Screen transitions
Open before you start:
1. **PowerPoint / Keynote in presentation mode** (primary screen)
2. **Colab notebook** already scrolled near the top (secondary)
3. Pre-position each target cell so that alt-tab lands on the right view

## Which cells to pre-position

| Slide | Target cell | What should be visible |
|-------|-------------|------------------------|
| 4 | Part 2.1 `perceive_classical` | The ~20-line function body |
| 4 | Part 2.2 `plan_bfs` | The BFS function (scroll down) |
| 5 | Part 3.1 `perceive_dl` | The `prompt = f"""..."""` part specifically |
| 5 | Part 3.2 `PolicyNet` + `make_policy_dataset` | Class definition + dataset builder |
| 7 | Part 5 Demo 1 output | The 3-panel figure (Input / Non-DL / DL) |
| 8 | Part 5 Demo 2 output | Rotated maze 3-panel figure |
| 9 | Part 5 Demo 3 output | Whichever demo best shows cascade; alternatively re-show the cascade_failure.png from the deck |
| 10 | Part 4.2 output | `=== Non-DL by Category ===` and `=== DL by Category ===` tables |

## Cut timing discipline
**Max 20 seconds per notebook cut.** If the audience is staring at code longer than that, they've stopped listening. The cut is a "proof of existence" — "yes the code looks like what I described" — not a code walkthrough.

## Delivery cadence
- Slow down on the Slide 11 math. "0.92 to the 20th power" and "about 19%" both deserve a half-beat pause.
- The Slide 9 reveal ("the DL agent still fails") is the story pivot. Drop your voice a notch, slow down, and let the notebook image speak for 3 seconds before saying anything.
- Don't rush Slide 12. The three numbered lessons are the grade.
- Full one-second silence before "Thank you."

## Time trims if you go over
Sections that can lose 10–15 seconds each without damage:
1. Slide 6 (Why not alternatives) — skip CNN answer, go directly to DQN
2. Slide 7 (Clean demo) — one sentence instead of two on the speed comparison
3. Slide 11 — skip the "observed 40% is higher because interior cells..." sentence
