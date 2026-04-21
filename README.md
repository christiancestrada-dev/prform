<div align="center">

```
██████╗ ██████╗ ███████╗ ██████╗ ██████╗ ███╗   ███╗
██╔══██╗██╔══██╗██╔════╝██╔═══██╗██╔══██╗████╗ ████║
██████╔╝██████╔╝█████╗  ██║   ██║██████╔╝██╔████╔██║
██╔═══╝ ██╔══██╗██╔══╝  ██║   ██║██╔══██╗██║╚██╔╝██║
██║     ██║  ██║██║     ╚██████╔╝██║  ██║██║ ╚═╝ ██║
╚═╝     ╚═╝  ╚═╝╚═╝      ╚═════╝ ╚═╝  ╚═╝╚═╝     ╚═╝
```

### **TRAIN SMARTER. PEAK ON RACE DAY.**

*The circadian performance optimizer built to put a PR on your next meet day.*

[![Next.js](https://img.shields.io/badge/Next.js-14-black?style=flat-square&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=flat-square&logo=typescript)](https://typescriptlang.org)
[![Prisma](https://img.shields.io/badge/Prisma-7-2D3748?style=flat-square&logo=prisma)](https://prisma.io)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3-06B6D4?style=flat-square&logo=tailwindcss)](https://tailwindcss.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-E8FF00?style=flat-square)](LICENSE)

</div>

---

## What Is PRform?

PRform is a performance optimizer for athletes. You input your weekly workout schedule and upcoming meet calendar, and the app works backward from race day, progressively shifting your circadian rhythm earlier so your reaction time, coordination, and endurance all peak at the starting gun.

It does this through two mechanisms. The first is a nightly sleep schedule that progressively advances your bedtime. The second is a PRC-based light prescription — using Phase Response Curve math to calculate the exact times during your day when bright light exposure will produce the largest circadian advance, and when evening light will delay it so you know to avoid it.

Beyond the schedule, PRform keeps you accountable with a live wind-down dashboard. Each evening it delivers a time-stamped behavioral countdown:

- **Dim your lights** — reduce lux before melatonin onset is due
- **Be active** — a light movement cue that accelerates core temperature drop post-exercise
- **Wind down** — eliminate cognitive stimulation before the sleep window opens
- **Time your meals** — last caloric intake timed to avoid thermogenic interference with sleep onset

These aren't rigid checkboxes — they're circadian entrainment tools. Each one sends a behavioral signal that helps anchor and shift your internal clock. If you miss a step or don't hit your target bedtime, the dashboard adapts in real time, recalculating your plan based on where you actually are, not where you were supposed to be.

The dashboard also gives you a daily peak performance window — not just on race day. Every day, you'll know the window when your reaction time, focus, and physical output are at their highest, so you can schedule your hardest workouts, drills, and mental prep around it.

**The race is won the week before. PRform builds the week.**

---

## Quick Start

```bash
# Clone and install
git clone https://github.com/PonchoCodes/prform.git
cd prform
npm install

# Set up the database
npx prisma migrate dev --name init

# Seed demo data
npx ts-node --compiler-options '{"module":"CommonJS"}' prisma/seed.ts

# Run
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

### Demo Account (ready to use immediately)

```
Email:    demo@prform.com
Password: demo1234
```

Pre-loaded with 8 weeks of training data, a weekly template, and three upcoming meets:
- **State Championships** in 18 days (A Race)
- **Local Invitational** in 6 days (B Race)
- **Regional Open** in 45 days (C Race)

---

## Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Framework | Next.js 14 App Router | Server components, API routes, file-based routing |
| Language | TypeScript 5 | Type-safe algorithm and data models |
| Styling | Tailwind CSS 3 | Design system tokens, zero-config utility classes |
| Database | SQLite via Prisma 7 | Zero-setup local DB with full ORM, Prisma v7 better-sqlite3 adapter |
| Auth | NextAuth.js v4 | JWT-based sessions, credentials provider |
| Charts | Recharts | Circadian performance curve and sleep ramp visualizations |
| Animation | Framer Motion + Tailwind keyframes | Page transitions, fade-up scroll, accent pulse, ticker |

---

## Application Pages

```
/                Landing page (dark hero, feature grid, marquee ticker)
/signup          Create account
/login           Sign in (demo credentials shown)
/onboarding      4-step setup wizard (profile, sleep, workouts, meets)
/dashboard       Primary view: today's peak window, tonight's bedtime,
                 wind-down countdown, weekly schedule, recovery score,
                 upcoming meets, and light prescription times
/schedule        Weekly template view + one-off workout logging + effort rating (1-5)
/meets           Full meet CRUD + 10-day sleep ramp chart per meet
/profile         Edit all profile data + wind-down notification toggles
```

---

## The Algorithm

> **File:** `lib/sleepAlgorithm.ts`
> **Signature:** `calculateSleepPlan(user, workouts, meets): DailySleepPlan[]`

Returns one `DailySleepPlan` object per day for the next 14 days. Each object contains: `recommendedBedtime`, `recommendedWakeTime`, `totalSleepHours`, `trainingLoadLevel`, `daysUntilNextMeet`, `recoveryScore`, `windDown` phase times, `peakWindowStart`, `peakWindowEnd`, and `lightPrescription`.

### Step 1: Base Sleep Need

Sleep need is determined by age, then adjusted for biological sex based on sex-specific differences in homeostatic sleep pressure documented in Burgard and Ailshire (2013).

| Age Range | Base Hours |
|---|---|
| 13-17 | 9.0 h |
| 18-25 | 8.5 h |
| 26+ | 8.0 h |
| Female athletes | +0.5 h |

### Step 2: Training Load Adjustment

Every night after a hard workout, your body requires additional slow-wave sleep to complete muscular repair and glycogen resynthesis. PRform adds sleep time proportional to that day's and the previous day's training load.

| Workout Type | Added Sleep |
|---|---|
| Rest / Easy Run / Cross Train | +0 min |
| Moderate Run | +15 min |
| Tempo / Track Workout | +20 min |
| Long Run | +30 min |
| Day AFTER any hard workout | +15 min additional |

### Step 3: Pre-Meet Circadian Phase Shift

The human circadian rhythm can be advanced through consistent, progressively earlier bedtimes in the days before a target event. PRform implements this phase-advance protocol automatically, counting backward from meet day with multipliers scaled to race priority.

```
Days Until Race Day    A Race    B Race    C Race
10 - 8 days           -15 min   -11 min   -8 min
7 - 5 days            -30 min   -23 min   -15 min
4 - 2 days            -45 min   -34 min   -23 min
Night before          -60 min   -45 min   -30 min
```

If multiple meets overlap their phase-shift windows, the highest-priority meet's values are applied.

### Step 4: PRC-Based Light Prescription

This is the core innovation.

Phase Response Curve (PRC) math tells us that light exposure produces different magnitudes of circadian phase shift depending on when it lands relative to your current sleep-wake cycle. Light in the morning (after your core body temperature minimum) advances the rhythm — shifts it earlier. Light in the evening delays it — pushes it later. PRform uses the PRC to calculate two things every day:

1. **Advance window** — the time in the morning when bright light (≥10,000 lux, 15–30 min) will produce the maximum phase advance toward your target race-day peak.
2. **Avoid window** — the evening hours when any light above ~10 lux will counteract the shift and delay your rhythm instead.

```
Light Prescription (example: 8-day advance toward 6:00 AM meet)

Day 8 before meet:   Advance window  06:45–07:15 AM   Avoid after 08:30 PM
Day 5 before meet:   Advance window  06:15–06:45 AM   Avoid after 08:00 PM
Day 2 before meet:   Advance window  05:45–06:15 AM   Avoid after 07:30 PM
Race eve:            Advance window  05:30–06:00 AM   Avoid after 07:00 PM
```

The advance window time shifts progressively earlier as meet day approaches, tracking the target phase advance. Both windows are stored in the `lightPrescription` field of `DailySleepPlan` and displayed on the dashboard as a daily action card.

### Step 5: Circadian Performance Curve

PRform models your moment-to-moment athletic output across the 24-hour day using a 5-component Gaussian sum fitted to circadian physiology literature (Facer-Childs & Brandstaetter, 2015; Valdez et al., 2019):

```
P(x) = 28
     + A₁·exp(-(x-μ₁)²/(2σ₁²))   [overnight trough,    μ=4.0h,  σ=2.2, A=−28]
     + A₂·exp(-(x-μ₂)²/(2σ₂²))   [morning secondary,   μ=10.5h, σ=2.0, A=+42]
     + A₃·exp(-(x-μ₃)²/(2σ₃²))   [post-lunch dip,      μ=14.5h, σ=1.1, A=−22]
     + A₄·exp(-(x-μ₄)²/(2σ₄²))   [afternoon peak,      μ=17.5h, σ=2.4, A=+52]
```

| Component | Role | μ (hour) | σ | A |
|---|---|---|---|---|
| Baseline | Floor | — | — | 28 |
| C₁ | Overnight trough | 4.0 | 2.2 | −28 |
| C₂ | Morning secondary peak | 10.5 | 2.0 | +42 |
| C₃ | Post-lunch dip | 14.5 | 1.1 | −22 |
| C₄ | Afternoon/evening peak | 17.5 | 2.4 | +52 |

The entire curve is shifted horizontally based on your wake time and target peak time. The phase shift δ is computed as:

```
wake_shift = (your_wake_time - population_avg_wake) × 0.85
nudge      = (target_peak - (natural_peak + wake_shift)) × 0.5
δ          = wake_shift + nudge
```

This gives you a personalized curve showing your exact relative performance percentage at every hour of the day — not a generic average.

### Step 6: Daily Peak Performance Window

The peak performance window is derived from the personalized curve: the contiguous window around the curve's daily maximum where output stays above 90% of peak. On the dashboard, this appears as a highlighted time range alongside an average performance percentage.

Athletes use this window to schedule their hardest interval sessions, race simulations, and pre-meet mental prep. On race day, the window narrows to a single projected peak time aligned to the competition start.

### Step 7: Wake Time and Total Sleep Calculation

```
Bedtime = WakeTime - TotalSleepNeed - PreMeetShift
```

Wake time stays fixed (your training schedule demands it). Bedtime moves earlier. Sleep opportunity expands. Recovery improves. Meet-day performance peaks.

### Step 8: Recovery Score (0-100)

A single composite score that tells you exactly how ready you are to run a PR.

```
Start:                                         100
Per consecutive hard day (no rest):             -5
Meet within 3 days:                            -10
Per hour of sleep deficit vs baseline:          -3
Per rest day in last 3 days:                    +5
Final range: clamped 0-100
```

### Step 9: Wind-Down Phase Times

Given a target bedtime T, four behavioral cue times are computed and stored:

| Phase | Time | Instruction |
|---|---|---|
| Dim your lights | T − 120 min | Drop overhead lights. Move to lamps only (≤100 lux). |
| Be active | T − 90 min | 10–15 min of light movement — walk, stretch, mobility. Accelerates the post-exercise core temperature drop that deepens sleep onset. |
| Wind down | T − 30 min | No screens. No content. Phone across the room. Begin slowing cognitive load. |
| Time your meals | T − 45 min | No caloric intake after this point. Meal timing is a zeitgeber — late eating delays your rhythm. |

These times are stored in the `DailySleepPlan` object and consumed in real time by the wind-down countdown component on the dashboard, which re-evaluates phase status every 60 seconds. If you miss a phase or fail to hit your target bedtime, the algorithm recalculates your plan from your actual position — not your projected one — and updates every downstream recommendation accordingly.

---

## The Wind-Down Protocol

PRform is not a white noise machine. It does not dim your lights. It does not block your apps. What it does is more powerful: it tells you exactly what to do, and exactly when, so that you build the behavioral consistency that physically moves your circadian phase earlier over multiple nights.

### Why consistency is more powerful than technology

Your circadian rhythm is not reset by any single device or supplement. It is entrained by the repeating pattern of light exposure, core temperature, meal timing, and behavioral cues across 5–7 consecutive nights. A perfect blackout room used only once before a race does almost nothing. The same basic behaviors repeated nightly for a week before a meet shifts your sleep phase by 30–90 minutes, which is measurable in reaction time, alertness, and performance output.

PRform structures the 2 hours before bed into a repeating behavioral sequence that your body learns to associate with pre-sleep physiology. This is classical conditioning applied to sleep performance.

### The science behind each phase

**Dim Your Lights (T − 120 min)**

Melatonin onset is suppressed by light at intensities above 10–100 lux, with the strongest effect at 480 nm (blue wavelength). Standard overhead LEDs typically measure 300–800 lux. Transitioning to desk lamps or table lamps, which average 30–100 lux, reduces light-mediated melatonin suppression by 70–90% and allows the natural melatonin ramp to begin on schedule.

Source: Brainard GC et al. (2001). Action spectrum for melatonin regulation in humans: evidence for a novel circadian photoreceptor. *Journal of Neuroscience*, 21(16), 6405–6412.

**Be Active (T − 90 min)**

A brief bout of light movement (walking, stretching, mobility work) 90 minutes before bed initiates a post-exercise core body temperature drop that is well-documented as a driver of sleep onset speed and slow-wave sleep depth. The temperature decline, not the movement itself, is the active ingredient — timing it here means the nadir coincides with lights-out.

Source: Horne JA, Staff LHE. (1983). Exercise and sleep: body-heating effects. *Sleep*, 6(1), 36–46.

**Wind Down (T − 30 min)**

Social media, messaging, and content consumption activate dopaminergic reward pathways that are neurochemically incompatible with the cortisol and core temperature decline required for sleep onset. Mere physical proximity to a smartphone reduces available cognitive capacity even when the device is face-down and silent. Distance removes the temptation loop entirely.

Source: Ward AF et al. (2017). Brain drain: the mere presence of one's own smartphone reduces available cognitive capacity. *Journal of the Association for Consumer Research*, 2(2), 140–154.

**Time Your Meals (T − 45 min)**

Meal timing is a secondary zeitgeber. Late eating raises core body temperature through thermogenesis and stimulates insulin secretion, both of which conflict with the physiological cooling and hormonal shifts that precede sleep onset. For athletes already in a circadian advance protocol, late meals act as a competing phase-delay signal. Cutting off caloric intake ~45 minutes before target bedtime eliminates this counter-signal.

Source: Wehrens SMT et al. (2017). Meal timing regulates the human circadian system. *Current Biology*, 27(12), 1768–1775.

---

## Adaptive Recalculation

If you miss your target bedtime or skip a wind-down phase, PRform does not reset to day one. It recalculates forward from your actual sleep time.

The algorithm re-runs nightly with your logged wake time and estimated sleep duration as inputs. If you slept later than planned, the phase shift schedule compresses: the remaining advance is distributed across fewer nights, which means each subsequent night's target bedtime moves earlier by a slightly larger increment. The light prescription windows update accordingly.

This prevents the common failure mode of abandoning a sleep plan after one bad night. You're always working from where you are, not where you were supposed to be.

---

## Design System

PRform mirrors the aesthetic of Citius Mag, the premier running media brand. Sharp, editorial, high-contrast. No softness.

```
Background:     #FFFFFF (pure white) / #0A0A0A (near black)
Primary text:   #0A0A0A
Accent:         #E8FF00 (electric yellow-green, used sparingly)
Secondary:      #6B6B6B
Borders:        #E5E5E5 (1px only, no shadows anywhere)

Headings:       DM Sans, weight 800-900, uppercase
Body:           DM Sans, weight 400, line-height 1.7
Data/times:     JetBrains Mono (all sleep times, countdowns, scores)

border-radius:  0 on everything
box-shadow:     none on everything
```

### Animations implemented

| Animation | Implementation |
|---|---|
| Fade-up on scroll | IntersectionObserver, 400ms ease-out, 80ms child stagger |
| Ticker marquee | CSS keyframes, pauses on hover |
| Number count-up | `requestAnimationFrame` loop, 600ms, triggers on viewport entry |
| Accent pulse | CSS keyframes, 2s loop, 0.85-1.0-0.85 opacity |
| Page transitions | Framer Motion `AnimatePresence`, 150ms opacity fade |
| Button press | `active:scale-[0.97]` via Tailwind |
| Link underline wipe | CSS pseudo-element `scaleX` transform, 200ms |
| Card hover | Border color transition to `#0A0A0A` |

---

## Database Schema

```prisma
model User {
  id              String    @id @default(cuid())
  email           String    @unique
  password        String    (bcrypt, 12 rounds)
  name            String?
  age             Int?
  biologicalSex   String?   ("male" | "female" | "other")
  weeklyMileage   String?   ("0-30" | "30-50" | "50-70" | "70+")
  experienceLevel String?   ("high_school" | "collegiate" | "post_collegiate" | "masters")
  currentWakeTime String?   (HH:MM 24h)
  currentBedTime  String?   (HH:MM 24h)
  restedFeeling   String?   ("well" | "sometimes" | "rarely")
  onboardingDone  Boolean   @default(false)
  notifPhase1-4   Boolean   @default(true)
  workouts        Workout[]
  meets           Meet[]
  sessions        Session[]
}

model Workout {
  id         String   (cuid)
  userId     String
  date       DateTime
  type       String   ("easy" | "moderate" | "tempo" | "long_run" | "track" | "race" | "rest" | "cross_train")
  distance   Float?
  duration   Int?     (minutes)
  effort     Int?     (1-5 perceived effort)
  isTemplate Boolean  (weekly template vs one-off)
  dayOfWeek  Int?     (0=Mon, 6=Sun, for templates)
}

model Meet {
  id        String
  userId    String
  name      String
  date      DateTime
  distances String   (freeform, e.g. "5K, 10K")
  priority  String   ("A" | "B" | "C")
}

model SleepLog {
  id             String   (cuid)
  userId         String
  date           DateTime
  targetBedtime  String   (HH:MM)
  actualBedtime  String?  (HH:MM — logged by user or inferred from wearable)
  targetWake     String   (HH:MM)
  actualWake     String?  (HH:MM)
  phasesCompleted Int     (0-4, count of wind-down phases hit)
}
```

---

## API Routes

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/register` | Create account (bcrypt hash, duplicate check) |
| ANY | `/api/auth/[...nextauth]` | NextAuth JWT handler |
| GET | `/api/sleep-plan` | Run algorithm, return full 14-day plan with peak windows and light prescription |
| GET/POST/PUT/DELETE | `/api/workouts` | Full workout CRUD |
| GET/POST/PUT/DELETE | `/api/meets` | Full meet CRUD |
| GET/PUT | `/api/user/profile` | Read and update user profile + notif prefs |
| POST | `/api/user/onboarding` | Save all onboarding data in one transaction |
| POST | `/api/sleep-log` | Log actual sleep times; triggers adaptive recalculation |

---

## Scientific References

1. **Mah CD, Mah KE, Kezirian EJ, Dement WC.** (2011). The effects of sleep extension on the athletic performance of collegiate basketball players. *Sleep*, 34(7), 943–950.
   > The foundational study establishing that sleep extension and phase advancement improves sprint speed (5%), reaction time, shooting accuracy, and subjective well-being in competitive athletes.

2. **Lewy AJ, Bauer VK, Ahmed S, et al.** (1998). The human phase response curve (PRC) to melatonin is about 12 hours out of phase with the PRC to light. *Chronobiology International*, 15(1), 71–83.
   > Establishes the PRC framework used to calculate light prescription windows. Morning light advances the rhythm; evening light delays it. The curve shape and magnitude inform PRform's advance and avoid window calculations.

3. **Facer-Childs ER, Brandstaetter R.** (2015). The impact of circadian phenotype and time since awakening on diurnal performance in athletes. *Current Biology*, 25(4), 518–522.
   > Documents the circadian performance curve shape across chronotypes and the magnitude of the afternoon performance peak relative to morning. Basis for the Gaussian performance model parameters.

4. **Valdez P, Ramírez C, García A.** (2019). Circadian rhythms in cognitive performance: implications for neuropsychological assessment. *ChronoPhysiology and Therapy*, 2012(2), 81–92.
   > Provides the multi-component model of cognitive and physical performance variation across the 24-hour day, validating the 5-Gaussian sum approach with independent data.

5. **Brainard GC, Hanifin JP, Greeson JM, et al.** (2001). Action spectrum for melatonin regulation in humans: evidence for a novel circadian photoreceptor. *Journal of Neuroscience*, 21(16), 6405–6412.
   > Quantifies the blue-light sensitivity of ipRGCs, providing the physiological basis for the Dim Lights phase and the avoid window in the light prescription.

6. **Chang AM, Aeschbach D, Duffy JF, Czeisler CA.** (2015). Evening use of light-emitting eReaders negatively affects sleep, circadian timing, and next-morning alertness. *PNAS*, 112(4), 1232–1237.
   > Documents melatonin delay, next-morning alertness reduction, and REM suppression from evening screen use. Basis for the Wind Down phase.

7. **Wehrens SMT, Christou S, Isherwood C, et al.** (2017). Meal timing regulates the human circadian system. *Current Biology*, 27(12), 1768–1775.
   > Establishes that meal timing is an independent circadian zeitgeber capable of shifting the peripheral clock by several hours. Basis for the Time Your Meals phase.

8. **Horne JA, Staff LHE.** (1983). Exercise and sleep: body-heating effects. *Sleep*, 6(1), 36–46.
   > Establishes that post-exercise core body temperature decline drives improved sleep onset and slow-wave depth. Basis for timing the Be Active phase at T − 90 min.

9. **Burgess HJ, Revell VL, Molina TA, Eastman CI.** (2010). Human phase response curves to three days of daily melatonin: 0.5 mg versus 3.0 mg. *Journal of Clinical Endocrinology and Metabolism*, 95(7), 3325–3331.
   > Supports the multi-night phase-shift strategy: circadian advancement requires 3–7 consecutive nights of shifted timing to produce measurable DLMO change. Informs the adaptive recalculation logic.

10. **Burgard SA, Ailshire JA.** (2013). Gender and time for sleep among U.S. adults. *American Sociological Review*, 78(1), 51–69.
    > Documents the sex-based difference in sleep duration that informs the +0.5h female athlete adjustment in the base sleep need calculation.

---

## Roadmap

- [ ] Push notifications (Service Worker + Web Push API)
- [ ] Wearable integrations (Garmin, Whoop, Oura) to auto-import actual sleep vs. target and trigger adaptive recalculation without manual logging
- [ ] Smart light integration (Philips Hue, LIFX) to execute the light prescription automatically at computed advance and avoid window times
- [ ] Group/team dashboard for coaches to monitor athlete recovery scores and peak windows
- [ ] Exportable race-week sleep plans (PDF) for athletes sharing with coaches

---

<div align="center">

**PRform**

Built for athletes who understand that the race is won the week before.

*Train Smarter. Peak on Race Day.*

</div>
