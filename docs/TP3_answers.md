# TP3 Answers

## Exercise 1
(a) scoreboard:local: ~263MB ; python:3.12-slim: ~178MB   
  
(b) Two things in build stage absent from runtime: pip, build tools (gcc, etc.)

(c) pip: not needed at runtime, removes an attack surface for supply-chain attacks.
    Build tools: compiled artifacts not needed; removing them prevents recompilation of exploits.

(d) COPY requirements.txt comes before COPY src/ so that the pip install layer is cached
    as long as requirements.txt does not change. The second build showed those steps as CACHED.

## Exercise 2
(a) The named volume sbdata makes writes possible despite --read-only.

(b) Named volume: data persists across container restarts and is managed by Docker, not exposed
    as a host path (avoids host permission issues and accidental deletion).

(c) --cap-drop ALL: attacker cannot use Linux capabilities (e.g. raw sockets, ptrace).

--security-opt no-new-privileges: attacker cannot escalate privileges via setuid binaries.

## Exercise 3
(a) sb stop time: ~: 1.915s ; sb2 stop time: ~10.490s

(b) When uvicorn is PID 1 it receives SIGTERM directly and shuts down cleanly.
    When sh is PID 1, sh does not forward SIGTERM to child processes; Docker waits the
    full 10-second timeout before sending SIGKILL.

(c) A rolling update of 20 replicas takes approximately 209.8s with a shell as PID 1, compared with 38.3s with Uvicorn as PID 1. This causes 171.5s of additional delay.

## Exercise 4
(a) 2 HIGH vulnerabilities and 0 CRITICAL vulnerabilities in same package: zlib1g

    --CVE-2026-76844 – HIGH
    --CVE-2026-85091 – HIGH

(b) pip-audit checks Python packages only. Trivy scans OS-level packages (Debian) in the
    base image, which pip-audit never sees.

(c) Pinning the scanner version ensures reproducible results; a newer version might report
    different findings and break the pipeline unexpectedly.

## Exercise 5
(a) git commit SHA → BUILD_SHA build-arg → ENV BUILD_SHA in image →
    uvicorn app reads BUILD_SHA at runtime → /version endpoint returns it.

(b) If both images share the tag latest, pulling latest gives no guarantee which version
    you get; a rollback or side-by-side comparison becomes impossible to distinguish.

## Screenshots
All screenshots are in docs/screenshots/:
- build_first.png: first docker build output
- size_output.png: image sizes
- build_cached.png: second build showing CACHED layers
- smoke_output.png: smoke.sh result
- container_id.png: docker exec id output
- readonly_check.png: read-only filesystem confirmation
- stop_sb.png: time docker stop sb
- stop_sb2.png: time docker stop sb2
- trivy_scan.png: trivy scan results
