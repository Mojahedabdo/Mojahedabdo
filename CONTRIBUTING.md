steps:
  - name: Checkout repository
    uses: actions/checkout@v4

  - name: Check that README.md exists and is not empty
    run: |
      echo "Checking README.md..."
      if [ ! -f README.md ]; then
        echo "::error::README.md is missing!"
        exit 1
      fi
      if [ ! -s README.md ]; then
        echo "::error::README.md is empty!"
        exit 1
      fi
      echo "✅ README.md exists and has content."
      echo "Lines in README.md: $(wc -l < README.md)"
      echo "Size: $(du -h README.md | cut -f1)"

  - name: Show first few lines of README (for quick inspection)
    run: |
      echo "=== First 20 lines of README.md ==="
      head -n 20 README.md
      echo "================================="

  - name: Basic Markdown structure check
    run: |
      echo "Checking for common Markdown issues..."
      # Check for unclosed code blocks (simple heuristic)
      OPEN=$(grep -c '```' README.md || true)
      if [ $((OPEN % 2)) -ne 0 ]; then
        echo "::warning::Odd number of code fences found. Possible unclosed code block."
      else
        echo "✅ Code fences look balanced."
      fi
      # Check that there is at least one heading
      if ! grep -qE '^#+ ' README.md; then
        echo "::warning::No Markdown headings found in README.md"
      else
        echo "✅ Headings found."
      fi

  - name: Success message
    run: |
      echo "🎉 Profile README validation completed successfully!"
