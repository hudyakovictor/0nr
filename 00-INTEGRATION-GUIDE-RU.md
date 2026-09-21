# Signal Arena — план интеграции Chart Workspace и Intelligence Hub в дизайн-систему

## Назначение пакета

В пакете находятся два runnable HTML-референса от разных дизайнеров:

1. `01-signal-arena-decision-workspace.html` — рабочее пространство анализа графика и принятия решения.
2. `02-signal-arena-intelligence-hub.html` — расширенная концепция главной страницы / Hub.

Их не нужно объединять между собой как два готовых приложения и не нужно сейчас переписывать под целевой стек. Задача текущей итерации — разобрать референсы на визуальные компоненты, состояния, layout patterns и motion presets, а затем добавить их в существующую дизайн-систему.

Текущая дизайн-система остаётся источником истины для:

- основной палитры;
- типографики;
- spacing;
- radius;
- elevation;
- базовых UI primitives;
- focus styles;
- общих motion durations.

HTML-файлы являются донорами визуальных и интерактивных решений, а не вторым источником токенов.

---

# 1. Главный принцип интеграции

Не копировать целиком:

- локальные `:root`;
- альтернативные Top Bar;
- всю навигацию;
- собственные цветовые шкалы;
- demo-данные;
- служебные Cloudflare scripts;
- продуктовую архитектуру целиком.

Переносить:

- композицию;
- компоненты;
- визуальные состояния;
- interaction patterns;
- motion ideas;
- responsive variants;
- спецификации слотов.

Каждому перенесённому элементу присвоить статус:

```text
CANONICAL — утверждён для основной системы
CANDIDATE — требует сравнения или упрощения
FUTURE — полезен после MVP
REJECTED — не использовать в продукте
REFERENCE — сохранить только как визуальный пример
```

---

# 2. Decision Workspace: что перенести

Файл:

```text
01-signal-arena-decision-workspace.html
```

Это наиболее ценный источник интерактивной части игры. В нём уже визуально описан цикл:

```text
контекст
→ изучение источников
→ доказательства
→ гипотеза
→ план
→ действие
→ уверенность
→ карты навыков
→ Seal
→ оценка
```

## 2.1 Chart Workspace — CANONICAL

Создать в дизайн-системе раздел `Chart & Decision` и компонент:

```text
DecisionChartWorkspace
```

Состав:

- свечной график;
- прошлые данные;
- t0;
- скрытая будущая область;
- selected candle;
- selected range;
- horizontal level;
- event marker;
- crosshair;
- tooltip;
- timeframe;
- loading;
- empty;
- error;
- revealed continuation.

Обязательные визуальные состояния:

```text
DEFAULT
TARGET_SELECTION
TARGET_CORRECT
TARGET_INCORRECT
PRE_DECISION
SEALED
REVEALING
REVEALED
ERROR
LOADING
```

До Seal будущие данные не показываются. После Seal будущая часть раскрывается по историческим данным.

## 2.2 Context Bar — CANDIDATE

Перенести компактную панель:

```text
режим
таймфрейм
источники
статус сценария
```

До Seal запрещено показывать:

- название реального актива;
- дату;
- имя сценария/Entity;
- будущие данные.

После Seal эти данные могут быть раскрыты как объяснение.

## 2.3 Evidence Source Tabs — CANONICAL

Оформить отдельный компонент источников:

```text
PRICE
VOLUME
ORDER FLOW
HIGHER TIMEFRAME
NEWS
PROJECT
```

Состояния:

- available;
- selected;
- inspected;
- used in rationale;
- unavailable;
- excluded;
- loading;
- error.

Не все источники обязаны присутствовать в каждом ScenarioPackage. Отсутствующий источник не заменяется fake data.

## 2.4 Decision Stepper — CANONICAL

Визуально выделить этапы:

```text
FACTS
CHART
DECISION
RATIONALE
INVALIDATION
SEAL
REVEAL
SCORE
```

Mobile-версия показывает текущий этап и общий progress. Desktop/tablet может показывать rail.

## 2.5 Evidence Cards — CANONICAL

Добавить specimen-карточки:

- observable fact;
- supporting evidence;
- contradicting evidence;
- weak evidence;
- excluded evidence;
- selected evidence;
- evidence unavailable.

У каждой карточки должны быть:

```text
source
label
value
status
selected state
```

## 2.6 Hypothesis — CANDIDATE

Сохранить визуальный паттерн выбора гипотезы, но не превращать его в обязательный новый раздел MVP до продуктового утверждения.

Состояния:

- empty;
- selected;
- edited;
- invalid;
- sealed.

## 2.7 Plan Builder — CANDIDATE

Из планировщика выделить визуальные элементы:

- entry condition;
- confirmation;
- invalidation;
- timeframe;
- risk note.

Для текущего MVP ключевым является `invalidation`. Остальные элементы можно сохранить в расширенном reference-наборе.

## 2.8 Decision Actions — CANONICAL

Основные действия:

```text
ENTER
WAIT
NO_TRADE
```

Состояния:

```text
DEFAULT
HOVER
PRESSED
FOCUSED
SELECTED
CONFIRMED
LOCKED
DISABLED
```

WAIT и NO_TRADE равноправны ENTER и не оформляются как отмена или вторичная кнопка.

## 2.9 Confidence Selector — CANDIDATE

Сохранить как дополнительный аналитический pattern:

```text
LOW
MEDIUM
HIGH
OVERCONFIDENT
```

Confidence не заменяет evidence и rationale.

## 2.10 Skill Hand — CANONICAL после замены ассетов

Bottom Sheet с картами полезен. При переносе заменить demo-карты на:

```text
skill-card-icons/c01…c40.svg
```

Зафиксировать состояния:

- available;
- selected;
- active;
- locked;
- new;
- upgraded;
- incompatible with current step.

Цветовые группы:

```css
--card-green:  #2E7F5C;
--card-yellow: #D0B24A;
--card-blue:   #4C6180;
--card-red:    #C56861;
```

## 2.11 Seal — CANONICAL

Состояния:

```text
NOT_READY
READY
CONFIRMING
SEALED
ERROR
```

После Seal нельзя менять:

- решение;
- rationale;
- evidence;
- invalidation.

## 2.12 Completion / Reward Scene — CANDIDATE

Сохранить staged-анимацию как high-intensity preset:

```text
motion.completion.chapter
```

Использовать только для:

- завершения главы;
- завершения полного Arena run;
- открытия значимого навыка.

Не запускать после каждого обычного ответа.

Добавить simplified и reduced-motion версии.

---

# 3. Decision Workspace: визуальные исправления

## 3.1 Использовать токены основной DS

Не переносить локальную палитру как новый source of truth. Перекрасить через существующие semantic tokens.

Базовые цвета:

```css
--bg: #070C14;
--bg2: #0A1120;
--surface: #0E1A30;
--card: #13243F;
--line: #1D3866;
--ink: #E8EEF7;
--dim: #8AA7C9;
--teal: #2EE6C8;
--up: #50C890;
--down: #EB635B;
--wait: #5AA9FF;
--amber: #F0A64D;
```

## 3.2 Использовать один Top Bar

Top Bar внутри HTML не переносить как самостоятельный компонент. В системе должен существовать один:

```text
SHARED_TOP_BAR_LOCKED
```

Слоты:

- LVL;
- XP;
- attempts;
- stars;
- coins;
- notification;
- settings.

## 3.3 Исправить размер текста

Минимумы:

```text
10px — только HUD metadata
11–12px — secondary/helper text
13–15px — основной текст
44px — минимальная touch-зона
```

Не использовать 7–9px для важных интерактивных подписей.

## 3.4 Responsive variants

Создать:

### Mobile

```text
Top Bar
Current Step
Chart
Current evidence/controls
Contextual CTA
Bottom Sheet для дополнительных источников
```

### Tablet/Desktop

```text
Stage Rail
Chart Workspace
Decision Inspector
```

Desktop workspace нельзя просто масштабировать до mobile.

---

# 4. Intelligence Hub: что перенести

Файл:

```text
02-signal-arena-intelligence-hub.html
```

Этот файл является расширенным запасом идей. В canonical Hub нужно перенести только часть.

## 4.1 Featured Scenario — CANONICAL

Состав:

- текущий сценарий;
- progress;
- следующий обязательный шаг;
- приблизительное время;
- primary CTA;
- краткий набор доступных фактов.

На mobile использовать один featured scenario, а не большой carousel из трёх маркетинговых слайдов.

## 4.2 Continue Card — CANONICAL

Типы:

- continue Arena run;
- start Rematch;
- continue Academy lesson.

Карточка должна содержать:

```text
route
state
progress
estimated time
next action
```

## 4.3 Daily Fix — CANDIDATE

Сохранить визуальный компонент короткой тренировки:

- конкретная ошибка;
- короткое упражнение;
- время;
- XP;
- серия.

Не делать Daily Fix обязательным retention-механизмом до отдельного продуктового решения.

## 4.4 Decision Profile Summary — CANONICAL

На Hub показывать только summary:

- process score;
- сильный навык;
- зона роста;
- ссылка в Profile или Academy.

Полную аналитику вынести из Hub.

## 4.5 Arena Modes — CANDIDATE

Для ближайшей версии показать:

```text
Scenario of the Day
Blind Scenario
Rematch
```

Сохранить как FUTURE:

```text
Post-Loss Protocol
Challenge Friend
```

## 4.6 Quick Metrics — CANONICAL с сокращением

На Hub максимум четыре метрики:

```text
Process Score
Completed Runs
Current Streak
Opened Skills
```

Не переносить восемь одновременных показателей в mobile Hub.

## 4.7 Analytics — перенести в Profile

Компоненты сохранить в дизайн-системе:

- score bars;
- activity heatmap;
- decision distribution;
- progress ring;
- recent activity.

Но canonical destination:

```text
Profile
```

## 4.8 Weak Skill Recommendation — перенести в Academy

Блок персонального пробела должен вести:

```text
Profile/Arena Debrief → конкретная тема Academy
```

## 4.9 Collection Progress — перенести в Collection

Показатели:

```text
18/40 cards
Entity mastery
new cards
active deck
```

не должны занимать основной Hub.

---

# 5. Recommended MVP Hub

## Desktop

```text
Top Bar
Featured Scenario
Quick Metrics (4)
Main Grid:
  Continue
  Daily Fix
Arena Modes (3)
Decision Profile Summary
```

## Mobile

```text
Top Bar
Featured Scenario
Continue
Daily Fix
Arena Modes — horizontal list
Personal Insight
Bottom Navigation
```

Не показывать на mobile одновременно:

- 8 metrics;
- full analytics;
- heatmap;
- donut;
- rankings;
- tournament;
- activity feed;
- five modes;
- hero carousel.

---

# 6. Что не включать в MVP Hub

Статус `FUTURE`:

```text
Tournament
Global Rank
Challenge Friend
Public Leaderboard
Events Feed
```

Статус `REJECTED` для ближайшего MVP:

```text
Shop
Paid tickets
Gambling-style rewards
Outcome-only scoring
Financial promises
```

Не удалять эти блоки из HTML-референса. Сохранить в разделе Future Product References.

---

# 7. Новые разделы дизайн-системы

Добавить:

## Chart & Decision

```text
Chart Workspace
Context Bar
Source Tabs
Evidence Card
Decision Stepper
Hypothesis
Plan Builder
Invalidation
Decision Actions
Confidence
Skill Hand
Seal
Reveal
Process Score
```

## Hub

```text
Featured Scenario
Continue Card
Daily Fix
Arena Mode Card
Quick Metric
Decision Profile Summary
Personal Insight
```

## Analytics

```text
Score Bars
Activity Heatmap
Decision Distribution
Progress Ring
Recent Activity
Empty Analytics
Loading Analytics
```

## Reward Motion

```text
Score count-up
Mastery Stars
Reward Flight
Chapter Completion
Skill Unlock
Reduced Motion Completion
```

---

# 8. Motion classification

Каждому motion preset присвоить интенсивность:

```text
LOW — press, select, tab, tooltip
MEDIUM — panel, route, sheet, Seal
HIGH — chapter complete, skill unlock, full run complete
```

На одном экране одновременно допускается:

```text
1 ambient animation
1 interaction/reward animation
```

Не переносить все бесконечные glow, float, spin и pulse одновременно.

Обязательно добавить `prefers-reduced-motion` и финальное стабильное состояние без animation dependency.

---

# 9. Asset provenance

Для Hub используются внешние backgrounds:

```text
assets/entities/liquidity_hydra.jpg
assets/entities/confirmation_bias_cult.jpg
assets/entities/headline_titan.jpg
```

Перед canonical-статусом:

- добавить файлы в asset repository;
- зафиксировать точные пути;
- определить crop/position;
- определить fallback;
- добавить hash/version;
- указать allowed slot.

Если архив ассетов недоступен, использовать:

```text
ASSET_ARCHIVE_NOT_EXTRACTED
```

Не подменять изображения случайными generated placeholders.

---

# 10. Обязательные visual specimens

Для каждого нового компонента показать:

```text
Default
Hover
Pressed
Focus
Selected
Disabled
Loading
Success
Warning
Error
Locked
Empty
```

Для мобильных компонентов показать минимум:

```text
320×568
360×800
390×844
412×915
```

Два preview-режима:

```text
Device Mockup — для презентации
Clean Preview — без корпуса телефона для handoff
```

---

# 11. Правила handoff

Не переписывать сейчас HTML под целевой движок.

Сначала завершить:

1. visual specimens;
2. states;
3. responsive variants;
4. token mapping;
5. motion presets;
6. asset slots;
7. component status;
8. clean screenshots.

Для каждого перенесённого элемента документировать:

```text
componentId
sourceHtml
sourceSelector
status
routeOwner
allowedSlots
states
mobileLayout
desktopLayout
motionPreset
assetDependencies
notes
```

---

# 12. Приоритет работ

## P0

1. Chart Workspace.
2. Evidence Source Tabs.
3. Decision Stepper.
4. Evidence Cards.
5. ENTER / WAIT / NO_TRADE.
6. Invalidation.
7. Seal.
8. Featured Scenario.
9. Continue Card.
10. Responsive mobile layout.

## P1

11. Skill Hand c01–c40.
12. Reveal.
13. Process Score.
14. Daily Fix.
15. Decision Profile Summary.
16. Quick Metrics.
17. Analytics components.
18. Reward Motion presets.

## P2

19. Rematch.
20. Blind Scenario.
21. Post-Loss Protocol.
22. Activity feed.
23. Expanded analytics.

## Future

24. Tournament.
25. Challenge Friend.
26. Global Rank.
27. Public Leaderboard.

---

# 13. Ожидаемый результат от автора дизайн-системы

В финале показать:

- новые разделы Chart & Decision, Hub, Analytics и Reward Motion;
- таблицу перенесённых компонентов;
- статусы CANONICAL/CANDIDATE/FUTURE/REJECTED;
- token mapping;
- mobile и desktop variants;
- clean previews;
- состояния каждого компонента;
- четыре viewport screenshots;
- motion intensity и reduced-motion вариант;
- список внешних ассетов;
- список элементов, которые не вошли в canonical MVP.

Не удалять исходные HTML-файлы: они остаются runnable visual references и нужны для сравнения при будущем переносе на целевой стек.
