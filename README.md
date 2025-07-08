# Baseball Simulator Project Summary

## Project Overview
Building a baseball game simulator in Rust using TDD (Test-Driven Development). The goal is to simulate entire seasons quickly and analyze statistical leaders (HR leaders, lowest ERA, best average, etc.).

I initiated this project after a discussion with Claude AI Sonnet 4.  I am relatively new to gaming and simulation software and wanted to figure out a set of abilities and attributes that would allow me to simulate a baseball game as well as learn rust and have fun.

This is a work in progress and I will be updating the README.md file as I go along.  I had the AI summarize our conversation below as reference.

## Key Design Decisions

### Architecture
- **GameEngine** - Main facade pattern (transaction boundary)
- **Pitcher vs Batter** - Starting with core mechanic first
- **Pitch-by-pitch simulation** - Like Diamond Mind Baseball
- **Count-dependent probabilities** - 0-0 count vs 3-2 count have different outcomes
- **Fictional players** but realistic baseball feel

### Player Attributes (0-100 scale, 50 = average)
**Batters:**
- Contact (0-100) - ability to make contact with ball
- Power (0-100) - extra base hits and home runs  
- Patience (0-100) - drawing walks, working counts
- Speed (0-100) - base running

**Pitchers:**
- Control (0-100) - strike throwing ability
- Stuff (0-100) - ability to get outs/strikeouts
- Stamina (0-100) - endurance
- Speed (0-100) - fielding/base running

### Testing Strategy
- **Integration tests** not unit tests
- **TDD approach** - one failing test at a time
- **AAA pattern** - Arrange, Act, Assert
- **"Loading the dice"** - force specific outcomes to test algorithm logic
- **Statistical testing** - large sample sizes with seeded randomness

## Technical Setup

### Project Structure
```
diamond_sim/
├── Cargo.toml
├── .vscode/
│   └── settings.json    # Format on save enabled
├── src/
│   ├── lib.rs          # Core library
│   └── main.rs         # Console runner
└── tests/
    └── at_bat_tests.rs # Integration tests
```

### Current Status
- Project structure created
- VS Code configured with rust-analyzer, CodeLLDB debugger
- Format on save enabled
- F5 debugging potentially configured
- First failing test written (compilation errors - expected)
- Ready to define basic structs and make test compile

### First Test
```rust
#[test]
fn test_average_pitcher_vs_average_batter_realistic_contact_rate() {
    // Arrange
    let pitcher = create_average_pitcher(); // 50 in all abilities
    let batter = create_average_batter();   // 50 in all abilities
    
    // Act - simulate many at-bats to get statistical reliability
    let outcomes = simulate_many_at_bats(pitcher, batter, 1000);
    
    // Assert - verify realistic contact rate for average vs average
    // MLB average contact rate is around 75-80%
    assert!(outcomes.contact_rate >= 0.70);
    assert!(outcomes.contact_rate <= 0.85);
}
```

## Key Problems Solved

### Probability Without Memory
- **Issue:** Each pitch needs count context (3-2 vs 0-0 are different)
- **Solution:** GameState includes count, different probability tables per situation

### Flaky Tests
- **Issue:** Random outcomes could exceed thresholds causing test failures
- **Solution:** Seeded random number generation for deterministic tests

### Player Representation
- **Approach:** Simple 0-100 ratings that create realistic statistical distributions
- **Inspiration:** Professional 20-80 scouting scale (50 = MLB average)

## Research Insights

### Professional Scouting (20-80 Scale)
- 50 = Major League Average
- Each 10 points = 1 standard deviation
- Position players: Hitting, Power, Running, Fielding, Throwing
- Maps perfectly to 0-100 scale

### Existing Simulations
- **Diamond Mind:** Pitch-by-pitch simulation with count dependency
- **Strat-O-Matic:** Card-based probabilities from real stats
- **APBA:** Dice-based outcomes with realistic distributions

## Next Steps (Immediate)
1. **Make first test compile:**
   - Define `Pitcher` struct with Control, Stuff, Stamina, Speed
   - Define `Batter` struct with Contact, Power, Patience, Speed  
   - Define `AtBatOutcomes` struct with contact_rate field
   - Define helper functions (create_average_pitcher, etc.)

2. **Implement basic GameEngine:**
   - Create `GameEngine` with `simulate_pitch()` method
   - Add basic probability calculations
   - Return realistic contact rates for average players

3. **Add count dependency:**
   - Define `GameState` struct with count information
   - Modify `simulate_pitch()` to accept game state
   - Implement different probabilities per count

4. **Expand testing:**
   - Add more realistic outcome assertions
   - Test edge cases (elite vs poor players)
   - Add integration tests for full at-bats

## Long-term Goals
- Simulate full games in seconds
- Run season simulations quickly
- Generate realistic season statistics
- Analyze league leaders (HR, ERA, AVG, etc.)

## Development Philosophy
- **TDD discipline** - no code until failing test
- **One feature at a time** - avoid building everything at once
- **Realistic outcomes** - verify statistical distributions match real baseball
- **Incremental complexity** - start simple, add features gradually

## Important Implementation Details

### Rust-Specific Considerations
- **No localStorage/sessionStorage** - Use in-memory storage only
- **Ownership/Borrowing** - Will need to handle passing players between functions
- **Error Handling** - Use Result<T, E> for functions that can fail
- **Modules** - Organize code into logical modules as it grows

### Testing Framework
- Using built-in Rust testing with `#[test]` attribute
- Integration tests in `tests/` folder automatically have access to lib.rs
- Tests should be deterministic (seeded RNG) to avoid flaky results
- Large sample sizes (1000+ simulations) for statistical reliability

### Expected Challenges
1. **Borrowing issues** when passing structs around
2. **Lifetime management** for game state
3. **Random number generation** - need to seed for consistent tests
4. **Statistical accuracy** - ensuring probabilities match real baseball

## Debugging Tips
- Use `cargo check` frequently while developing
- `println!` debugging is fine for learning
- rust-analyzer in VS Code provides inline error messages
- `cargo test -- --nocapture` shows println output in tests

## Research References
- **Strat-O-Matic:** Card-based simulation with individual player cards
- **Diamond Mind Baseball:** Pitch-by-pitch simulation with count dependency
- **20-80 Scouting Scale:** Professional player evaluation system
- **APBA:** Dice-based outcomes with realistic statistical distributions

## Future Features (Don't Build Yet)
- Fatigue system for pitchers
- Ballpark effects (dimensions, weather)
- Platoon splits (L/R matchups)
- Defensive positioning
- Clutch/pressure situations
- Injury system
- Season-long player development

## Commands
```bash
cargo test         # Run tests
cargo run          # Run console app
cargo check        # Check compilation
cargo build        # Build project
cargo test -- --nocapture  # Show println output in tests
```

## Context for AI Assistants
- **Experience:** 20 years C# development, 3 months Rust (with gaps)
- **Approach:** Methodical, test-driven, step-by-step
- **Goal:** Learn Rust while building realistic baseball simulator
- **Focus:** Understanding concepts, not just getting code that works
- **Preference:** Explain Rust concepts as they come up naturally
- **Testing Philosophy:** Integration tests over unit tests, realistic scenarios
- **Architecture:** Facade pattern (GameEngine) as main entry point