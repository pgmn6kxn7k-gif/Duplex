step:
  type: shell
  cmd: |
    set -e
    echo ">>> Hardening {ctx.item}"
    # 1. Vulnerability scan on the cloned repo
    trivy fs --exit-code 1 {ctx.tmp}
    # 2. Static analysis (only if C/C++ files exist)
    find {ctx.tmp} -name '*.c' -o -name '*.cpp' | xargs -r cppcheck --enable=all --error-exitcode=1
    # 3. Example artefact sign (use your own registry/tag logic)
    # cosign sign --key env://COSIGN_KEY ghcr.io/org/{ctx.item}:$(git -C {ctx.tmp} rev-parse --short HEAD)
