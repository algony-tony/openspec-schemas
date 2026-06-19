# Tasks: [Release version]

## 1. Cut
- [ ] 1.1 Bump version, update changelog, tag the release commit

## 2. Stage
- [ ] 2.1 Deploy to staging
- [ ] 2.2 Smoke test staging — expect pass

## 3. Roll out
- [ ] 3.1 Canary / first stage; monitor signals against rollout criteria
- [ ] 3.2 Promote to full rollout (or roll back if a trigger fires)

## 4. Verify
- [ ] 4.1 Production smoke + monitor dashboards/alerts
- [ ] 4.2 Send comms / announcement
