---
title: "Descriptive vs Prescriptive Testing: Transforming Code Reviews Through Better Test Design"
date: 2025-08-20
slug: "descriptive-vs-prescriptive-testing-transforming-code-reviews-through-better-tes"
tags: ["testing", "code review", "best practices"]
description: "*How writing tests that describe behavior instead of implementation can revolutionize your code review process*"
---

*How writing tests that describe behavior instead of implementation can revolutionize your code review process*

## The Traditional Code Review Problem

Most development teams follow a familiar code review pattern:

1. Check if tests exist ✅
2. Review implementation details line by line 👀
3. Debate formatting and style choices 💬
4. Try to understand what the code is supposed to do 🤔
5. Hope the tests actually validate the right behavior 🤞

This process is slow, error-prone, and often misses the forest for the trees. But what if we could flip this script entirely?

## The Game-Changing Insight

**The tests should tell the story of what the code does, not how it does it.**

When tests are written descriptively (focusing on behavior) rather than prescriptively (focusing on implementation), they become living documentation that transforms the entire code review workflow.

## Prescriptive vs Descriptive Testing: A Real Example

### ❌ Prescriptive Testing (Implementation-Focused)

```
def test_get_dq_approach_examples():
    """Test that get_dq_approach_examples returns correct structure."""
    result = get_dq_approach_examples()
    
    # Testing implementation details
    assert isinstance(result, dict)
    assert "examples" in result
    assert len(result["examples"]) > 0
    assert all("scenario" in ex for ex in result["examples"])
    assert all("approach" in ex for ex in result["examples"])

```

**Problems with this approach:**

- Tests break when internal structure changes
- Reviewers learn nothing about intended behavior
- No indication of what value this provides users
- Focused on "how" instead of "what"
  
![Distracted boyfriend meme: Boyfriend (Developer) looking at "Testing implementation details" while girlfriend "Testing user behavior" looks disapproving, and the woman he's looking at is labeled "Brittle tests that break on refactor"](/images/posts/blogger-5939c0cfc6.png)  

### ✅ Descriptive Testing (Behavior-Focused)

```
def test_users_can_find_appropriate_dq_approach_for_their_scenario():
    """Users should be able to find the right data quality approach for their specific use case."""
    examples = get_dq_approach_examples()
    
    # Verify users get practical guidance
    assert "examples" in examples, "Users need concrete examples to learn from"
    example_list = examples["examples"]
    assert len(example_list) > 0, "Users need at least one example to understand the concept"
    
    # Verify each example provides actionable guidance
    for example in example_list:
        assert "scenario" in example, "Users need to understand when to apply each approach"
        assert "approach" in example, "Users need clear guidance on which approach to use"
        
        # Verify content quality for user value
        assert len(example["scenario"]) > 10, "Scenarios must be detailed enough to be useful"
        assert example["approach"] in ["pyspark", "json_dq_framework"], "Approach must be a valid option"

```

**Benefits of this approach:**

- Tests survive refactoring because they focus on outcomes
- Reviewers immediately understand user value
- Tests document the intended behavior
- Implementation can change without breaking tests

## The Transformed Code Review Workflow

With descriptive tests, code reviews become a three-stage pipeline:

  
![Galaxy brain meme: Small brain - "Manual code review for everything" / Medium brain - "Automated formatting checks" / Large brain - "Tests document behavior" / Galaxy brain - "Three-stage pipeline: Automation → Behavior → Implementation"](/images/posts/blogger-8c25016afc.png)[](/images/posts/blogger-2e72488f1d.png)

### Stage 1: Automated Validation (CI/CD)

- ✅ **Tests exist and pass** (automated)
- ✅ **Code formatting/style** (automated via linting)
- ✅ **Build succeeds** (automated)

### Stage 2: Behavioral Review (Human - Fast)

- 👀 **Read the test descriptions** to understand intended behavior
- 🎯 **Validate business logic** makes sense
- 📋 **Check edge cases** are covered

### Stage 3: Implementation Review (Human - Focused)

- 🔍 **Review the "how"** with full context of the "what"
- 🚀 **Focus on performance, security, maintainability**
- 💡 **Suggest alternative implementations** if needed

## The BDD Connection: Behavior Is What Matters

This approach naturally borders on Behavior-Driven Development (BDD) because **behavior is where the real value lies**. Consider these test names:

```
# Prescriptive (focuses on process)
def test_function_returns_dict_with_examples_key():

# Descriptive/BDD-ish (focuses on behavior)  
def test_users_can_discover_appropriate_dq_approach_for_their_scenario():

```

The second approach tells you:

- **Who** benefits (users)
- **What** they can accomplish (discover appropriate approach)
- **When** they'd use it (for their specific scenario)
- **Why** it matters (appropriate choice is important)

## Practical Implementation Tips

### 1. Write Test Names as User Stories

```
# Instead of: test_validate_input_parameters()
def test_users_get_clear_error_when_providing_invalid_input():

# Instead of: test_generate_json_rule()  
def test_analysts_can_create_monitoring_rules_for_data_quality():

```

### 2. Use Descriptive Assertion Messages

```
# Instead of: assert result is not None
assert result is not None, "Users must receive guidance, not empty responses"

# Instead of: assert len(items) > 0
assert len(items) > 0, "Users need concrete examples to understand the concept"

```

### 3. Group Tests by User Capability

```
class TestUserCanAnalyzeDataQuality:
    def test_identifies_quality_issues_in_sample_data(self):
    def test_provides_actionable_remediation_suggestions(self):
    def test_handles_edge_cases_gracefully(self):

```

### 4. Test Outcomes, Not Internals

```
# Don't test: "function calls database.query()"
# Do test: "users can retrieve their recent transactions"

# Don't test: "returns Response object with status 200"  
# Do test: "users receive confirmation when operation succeeds"

```

## The Retroactive Testing Reality

While true TDD writes tests first, most teams write tests retroactively. That's okay! The key insight is that **even retroactive tests should be descriptive, not prescriptive.**

******![](/images/posts/blogger-0d53616d0d.png)[](/images/posts/blogger-c5e5dbcc28.png)**

When writing tests after implementation:

1. **Forget the implementation** temporarily
2. **Think like a user** - what should this accomplish?
3. **Write the test name** as a user capability
4. **Assert on outcomes** that matter to users
5. **Add failure messages** that explain user impact

## Real-World Benefits We've Observed

From our recent refactoring of prescriptive tests to descriptive ones:

### Before (Prescriptive)

- 🐌 **Slow reviews**: "What does this code actually do?"
- 🔴 **Brittle tests**: Break on every refactor
- 😕 **Poor documentation**: Tests don't explain purpose
- 🤷 **Unclear value**: Hard to justify features

### After (Descriptive)

- ⚡ **Fast reviews**: Behavior is clear from test names
- 💚 **Resilient tests**: Survive implementation changes
- 📚 **Living docs**: Tests explain user value
- 🎯 **Clear purpose**: Easy to validate features matter
![](/images/posts/blogger-7106113323.png)[](/images/posts/blogger-145960a1c3.png)

## The Bottom Line

**Tests are not just validation - they're communication.**

When tests describe behavior instead of implementation, they become:

- 📖 **Documentation** that stays current
- 🛡️ **Protection** that survives refactoring
- 🗺️ **Roadmap** for code reviewers
- 💎 **Specification** of user value

## Getting Started

1. **Audit existing tests**: Look for implementation details being tested
2. **Rewrite test names**: Focus on user capabilities
3. **Update assertions**: Test outcomes, not internals
4. **Add context**: Explain why failures matter to users
5. **Iterate**: Each new test should follow descriptive patterns

The goal isn't perfect TDD or BDD - it's tests that make your code reviews faster, your refactoring safer, and your team's understanding clearer.

Because at the end of the day, we rarely care about the process. We care about the behavior.

---

*This insight emerged from refactoring a test suite from prescriptive to descriptive patterns. The difference in code review speed and clarity was immediately apparent - and transformative.*
