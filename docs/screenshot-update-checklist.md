# Screenshot refresh checklist

Existing images are **historical evidence of the original implementation**, not proof that revised rules are deployed or validated.

## Can be retained
- `sentinel_soc_architecture.png` and `architecture_editor_view.png` if the architecture is unchanged.
- `sentinel_incident_example.png` as an explicitly labeled **earlier lab incident example**; do not imply it came from revised KQL.

## Replace or supplement after redeployment
- `rule1_configuration.png`: old rule logic / threshold; take a new screenshot after updating the analytic rule.
- `rule2_configuration.png`: old correlation logic; take a new screenshot.
- `rule3_query.png`: refresh if deploying revised lockout query.
- `query_detection_results.png`: re-run updated rules and capture verified results.
- `rule3_entity_mapping.png`: confirm account/computer mapping still matches the output.
- `repo_kql_rules.png`, `final_repo_view.png`, `screenshots-01-repo-structure.png`, `incident_response_guide.png`: repository/documentation UI snapshots may be outdated; optional refresh.

**Do not edit old screenshots to make them look like new test results.** Capture new images from the actual Sentinel workspace after validation.
