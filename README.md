# Siege of Shards

**An MR strategy game in the spirit of Clash Royale, where the arena unfolds right in your room.**
You stand above the battlefield in mixed reality, pick up a card with your hand and throw a squad onto the field. You play against an AI that reads the situation on the field instead of spawning units at random.

<p align="left">
  <img alt="Unity" src="https://img.shields.io/badge/Unity-6000.0.58f1-000?logo=unity">
  <img alt="URP" src="https://img.shields.io/badge/URP-17.2-2b7">
  <img alt="OpenXR" src="https://img.shields.io/badge/OpenXR-Meta%20XR%2081-1a73e8">
  <img alt="Platform" src="https://img.shields.io/badge/Platform-Meta%20Quest%202%2F3%2FPro-6a1b9a">
</p>

<p align="center">
  <img src="Presentation/preview/Preview.png" alt="Siege of Shards" width="820">
</p>

---

## Screenshots

<p align="center">
  <img src="Presentation/preview/PreviewGameplay2.png" alt="Arena on the floor of a real room" width="900">
</p>

<p align="center"><i>The arena stays where you placed it. The room remains visible around the battlefield.</i></p>

| Close-up battle | Main menu |
|---|---|
| <img src="Presentation/preview/PreviewGameplay.png" alt="Cards in hand and the summon circle" width="440"> | <img src="Presentation/preview/PreviewMenu.png" alt="Menu, deck and chest" width="440"> |
| A hand of four cards, the elixir bar and the summon circle under the controller ray. | Deck, gold and chests on a curved panel in space. |

---

## Match Rules

- The arena is placed on a real surface in your room. The timer and the AI start only after you confirm the spot.
- **3 minutes**, **3 towers** per side.
- Elixir: starts at 5, max 10, +1 every 2 seconds. The AI has the same limit.
- Victory: destroy all three enemy towers or keep more of your own when the timer runs out.
- Match rewards: win `40 gold + chest`, draw `20`, loss `10`.

## Mixed Reality

- **Room scanning** via Meta Scene Capture and MR Utility Kit, with a search for a suitable horizontal surface.
- **Arena placement** with the controller: semi-transparent preview, dragging, confirmation. The `OnArenaConfirmed` event is the start signal for all battle logic.
- **NavMesh is baked at runtime** after the arena is placed, so unit navigation works in any room and at any table scale.
- **Cards are physical objects.** Grip takes a card from the hand, the controller ray highlights the summon point with a circle, and releasing places the squad. Spells can be cast anywhere on the field, units only in your own zone.

## Deck

The player's deck has 8 cards, 4 of them in hand; a used card goes to the end of the queue.

| Card | Type | Elixir | Feature |
|---|---|---|---|
| Skeletons | units | 1 | cheap trade, distraction |
| Heal | spell | 3 | area healing |
| Cowboy | unit | 4 | hits air targets |
| Fire Dragon | unit | 4 | ground unit, hits air targets |
| Meteor | spell | 4 | area damage |
| Explosion | spell | 4 | delayed explosion |
| Freezy | spell | 4 | area freeze |
| Centaur | unit | 5 | ground striker |
| Cold Dragon | unit | 5 | flying, hits air targets |
| Lazer | spell | 5 | directional area damage |
| Golem | unit | 7 | tank, pressure on towers |

## AI Opponent

The AI follows the same rules as the player: the same elixir economy and the same 4 + 4 card cycle.

- **BattlefieldAnalyzer** counts each side's units, positions, distances to towers and the direction of the player's attack every tick, and reduces it to five threat levels.
- **SmartAIOpponent** picks one of four strategies: `Defend`, `CounterAttack`, `Aggressive`, `EconomyBuild`.
- **AICardSelector** ranks the cards in hand by priority and picks a summon point. A flying attack gives a big bonus to cards with `canTargetAir`, enemy clusters favor spells.
- Elixir reserve, "thinking" delay and the player passivity threshold are set in the Inspector.

## Units

Each unit type has its own finite state machine (`Run → Attack → Death`) on top of a shared `UnitStateManager` with a NavMesh agent, aggro radius and team. Flying units use their own `FlyingAgent`, which takes the path from the NavMesh but keeps its altitude itself. Freezing, healing and damage zones are implemented as effects on top of the shared `Health`.

## Meta Progression

- The deck is built in the main menu from the full card set.
- Gold and chest opening attempts are saved between sessions.
- Cards are upgraded with fragments: 10 fragments + gold raise the card level.
- Chests give fragments and new cards with an opening animation.

## Tech Stack

Unity 6 (URP) · OpenXR + Meta XR SDK 81 · XR Interaction Toolkit 3.2 · AR Foundation 6.3 · MR Utility Kit · Unity AI Navigation · Cinemachine · Input System

## Project Structure

```
Assets/Scripts/
├─ AI Opponent/      battlefield analysis, strategy and card choice, debug overlay
├─ Cards/            card data (ScriptableObject), elixir, XR dragging
├─ CinematicLoading/ dragon flight over background loading of the battle scene
├─ LogicBattle/      match timer, win conditions, rewards, pause
├─ MainMenu/         deck, gold, chests, card upgrades
├─ MR/ MR2.0/ MR3.0/ room scanning and arena placement
├─ NavMesh/          runtime navigation baking
├─ Tower/            tower health and destruction
└─ Units/            unit FSM, health, spells and effect zones
```

Scenes: `LoadingScreen` → `MainMenu` → `BattleMatch`. The `CinematicLoading` scene with a dragon flight over background battle loading is ready but currently disabled in Build Settings.

---

© D.Baranov Universe
