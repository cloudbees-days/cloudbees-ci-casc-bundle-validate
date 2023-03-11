# cloudbees-ci-casc-bundle-validate
Jenkins Pipeline used to validate CloudBees CI CasC bundles.

***jq*** query to get bundle validity: `.validations[] | select(.bundle=="{BUNDLE_NAME_ID}") | .result.valid`
