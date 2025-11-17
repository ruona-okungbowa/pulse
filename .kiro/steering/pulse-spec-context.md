---
inclusion: always
---

# Pulse Spec Context - Steering Document

## Project Overview

Pulse is a team health monitoring platform that replaces synchronous standup meetings with asynchronous 30-second check-ins. The system uses AI to detect burnout signals by analyzing work patterns and provides managers with actionable insights.

## Core Philosophy

**Focus on work problems, not feelings:**

- Ask about workload, clarity, blockers, and support needs
- Do NOT rely heavily on mood/energy (people lie about feelings)
- Blockers are the strongest burnout signal (people are honest when they want help)

## Key Differentiators vs. Competitors (Geekbot, DailyBee)

1. **AI Burnout Detection** - Analyzes patterns, not just collects data
2. **Mobile-First** - Beautiful mobile app, not just Slack
3. **Blocker-Centric** - Persistent blocker tracking is core feature
4. **Proactive Alerts** - Tells managers WHO needs help and WHAT to do
5. **Work-Focused Questions** - Reduces lying, increases honesty

## Check-In Flow (30 seconds)

1. **Workload**: manageable / challenging / overwhelming
2. **Clarity**: clear / somewhat clear / confused
3. **Focus**: text input (what you're working on)
4. **Blockers**: text input (MOST IMPORTANT - people are honest here)
5. **Support**: optional (do you have what you need?)

**NO mood slider. NO energy level.**

## AI Detection Strategy

### Primary Signals (Hard to Fake)

1. **Persistent Blockers** - Same blocker 3+ times over 14+ days (STRONGEST SIGNAL)
2. **Overwhelming Workload** - Reported 5+ times in 30 days
3. **Disengagement** - Check-in rate drops from 70%+ to <40%
4. **Late-Night Work** - Checking in after 9pm repeatedly
5. **Lack of Clarity** - Confused on priorities 5+ times

### Secondary Signals (Use for Context)

6. **Text Sentiment** - Negative language in focus/blockers
7. **Response Completeness** - Skipping more fields than usual

### What We DON'T Do

- ❌ Don't rely on self-reported mood (people lie)
- ❌ Don't alert on single data points (need patterns)
- ❌ Don't diagnose burnout (only flag concerning patterns)
- ❌ Don't automate actions (manager always decides)

## Technical Stack

**Mobile**: React Native (Expo)
**Web**: Next.js 14
**Backend**: Supabase (PostgreSQL + Real-time + Auth)
**AI**: Custom Kiro MCP Server + OpenAI GPT-4
**Integrations**: Slack API, SendGrid, Stripe

## Kiro Integration Requirements

### 1. Custom MCP Server

**Name**: TeamHealthAnalyzer
**Capabilities**:

- detectPersistentBlockers() - Semantic similarity analysis
- analyzeBurnoutRisk() - Multi-signal pattern detection
- generateManagerAlert() - Actionable alert creation
- generate1on1Prep() - Talking points for managers

### 2. Spec-Driven Development

- Comprehensive requirements (EARS format)
- Detailed design (architecture, components, data models)
- Implementation tasks (30+ tasks with sub-tasks)

### 3. Agent Hooks

- on-checkin-submit - Analyze blocker patterns
- on-daily-9am - Send reminders
- on-burnout-detected - Alert manager
- on-blocker-resolved - Celebrate resolution
- on-team-milestone - Team celebrations

### 4. Steering Docs

- burnout-research.md - Psychology and indicators
- blocker-analysis.md - Detection techniques
- ai-prompts.md - Prompt templates
- ui-design-system.md - Design guidelines

### 5. Vibe Coding

- Rapid UI prototyping
- Slack integration generation
- Chart components
- API routes

## Design Principles

**Professional, Not Playful**:

- Clean, modern, trustworthy
- Calming colors (blues, purples)
- Smooth animations (purposeful, not gimmicky)
- Accessible (WCAG AA compliant)

**Mobile-First**:

- Check-ins optimized for mobile
- Dashboard optimized for web
- Both platforms have full feature parity

**Privacy-Focused**:

- Aggregate data for team metrics
- Individual alerts anonymize sensitive data
- Managers see patterns, not raw mood scores

## Business Model

**Free**: Teams up to 10
**Pro**: £8/user/month (AI insights, mobile, Slack)
**Enterprise**: £15/user/month (SSO, advanced analytics)

**Target Market**: Remote/hybrid teams (5-50 people), tech companies, startups

## Hackathon Strategy

**Target Prizes**:

1. Overall 1st Place ($30K) - Primary target
2. Best Startup Project ($10K) - Strong contender
3. Frankenstein ($5K) - Stitches together async + AI + health

**Winning Formula**:

- Show deep Kiro mastery (all 5 features)
- Demonstrate real traction (10 teams, £480 MRR)
- Prove differentiation (not just "Geekbot clone")
- Beautiful, polished execution

## 18-Day Build Timeline

**Week 1**: Foundation (specs, mobile check-in, web dashboard basics)
**Week 2**: Core features (AI detection, Slack integration, hooks)
**Week 3**: Polish & traction (get 10 teams, 3 paying, demo video)

## Key Success Metrics

**For Hackathon**:

- 10 teams signed up
- 3 teams committed to paying (£480 MRR)
- Working mobile + web app
- Custom MCP server functioning
- All Kiro features demonstrated

**For Startup**:

- Month 6: 500 users (£4K MRR)
- Month 12: 2,000 users (£16K MRR)
- Year 2: 10,000 users (£80K MRR)

## Important Constraints

**What to Build (MVP)**:

- ✅ Mobile check-in flow
- ✅ Web manager dashboard
- ✅ AI burnout detection
- ✅ Slack integration
- ✅ Push notifications
- ✅ Custom MCP server

**What to Skip (Post-Hackathon)**:

- ❌ Voice input
- ❌ Video testimonials
- ❌ Spooky UI theme
- ❌ Advanced gamification
- ❌ SSO
- ❌ Mobile manager dashboard (web only for MVP)

## Validation Questions for Technical Managers

When showing this to technical managers, ask:

1. Would you use this for your team? Why or why not?
2. Is £8/user/month reasonable pricing?
3. Do you agree persistent blockers are a strong burnout signal?
4. What's missing that would make you pay for this?
5. Privacy concerns - would your team be comfortable?

## Remember

- **Focus on blockers** - This is our unique insight
- **Work-focused, not feelings-focused** - Reduces lying
- **Actionable alerts** - Tell managers exactly what to do
- **Deep Kiro integration** - Show mastery, not just usage
- **Real traction** - Get paying customers during hackathon
