# TEST APPLICATION FOR GITSTREAM


# -*- mode: yaml -*-

# +----------------------------------------------------------------------------+
# | WARNING: This file controls repo automations, use caution when modifying   |
# +----------------------------------------------------------------------------+
# | This file contains one or more /:\ gitStream automations:                  |
# | https:// docs.gitstream.cm                                                 |
# |                                                                            |
# | gitStream uses YAML syntax with nunjucks templating via Jinja 2.           |
# |                                                                            |
# | Automations follow an "if this, then that" execution format.               |
# | More info here: https://docs.gitstream.cm/how-it-works/                    |
# |                                                                            |
# +----------------------------------------------------------------------------+

# /:\ gitStream Reference Docs: 
#    Context Variables: https://docs.gitstream.cm/context-variables/
#    Filter Functions: https://docs.gitstream.cm/filter-functions/
#    Automation Actions: https://docs.gitstream.cm/automation-actions/

manifest:
  version: 1.0
automations:
  # Add a label that indicates how many minutes it will take to review the PR.
  estimated_time_to_review: 
    if:
      - true
    run:
      - action: add-label@v1
      # etr is defined in the last section of this example
        args:
          label: "{{ calc.etr }} min review"
          color: {{ 'E94637' if (calc.etr >= 20) else ('FBBD10' if (calc.etr >= 5) else '36A853') }}
  # Post a comment that lists the best experts for the files that were modified.
  explain_code_experts:
    if:
      - true
    run:
      - action: explain-code-experts@v1 
        args:
          gt: 10 
  # # Automaticly merge when changes are considered safe
  # safe_changes:
  #   # Triggered for any changes that only affect formatting, documentation, tests, or images
  #   if:
  #     - {{ is.formatting or is.docs or is.tests or is.image or is.onlyTranslations }}
  #   # Apply a safe change label, approve the PR and explain why in a comment.
  #   run: 
  #     - action: add-label@v1
  #       args:
  #         label: 'safe-change'
  #     - action: approve@v1
  #     - action: add-comment@v1
  #       args:
  #         comment: |
  #           This PR is considered a safe change and has been automatically approved.
  # # Automaticly merge tiny changes (a change is tiny when less then 5 lines)
  # approve_tiny_change:
  #   # Triggered for PRs that contain one file and one line.
  #   if:
  #     - {{ is.tiny }}
  #   run:
  #     - action: add-label@v1
  #       args:
  #         label: 'single-line'
  #     - action: approve@v1
  #     - action: add-comment@v1
  #       args:
  #         comment: |
  #           This PR has been approved because it is only a single line
  # # Add double review policy when multiple disciplines 
  # double_review:
  #   if:
  #     - {{ is.backend and is.frontend }}
  #   run:
  #     - action: set-required-approvals@v1
  #       args:
  #         approvals: 2

# The next function calculates the estimated time to review and makes it available in the automation above.
calc:
  etr: {{ branch | estimatedReviewTime }}

# test

# changes:
#   # Sum all the lines added in the PR
#   additions: {{ branch.diff.files_metadata | map(attr='additions') | sum }}
#   # Sum all the line removed in the PR
#   deletions: {{ branch.diff.files_metadata | map(attr='deletions') | sum }}

# is:
#   formatting: {{ source.diff.files | isFormattingChange }}
#   docs: {{ files | allDocs }}
#   tests: {{ files | allTests }}
#   image: {{ files | allImages }}
#   onlyTranslations: {{ files | match(regex=r/translations\//) | all }}
#   backend: {{ files | match(regex=r/backend\//) | some }}
#   bff: {{ files | match(regex=r/bff\//) | some }}
#   frontend: {{ files | match(regex=r/frontend\//) | some }}
#   tiny: {{ changes.additions - changes.deletions <= 5 }}