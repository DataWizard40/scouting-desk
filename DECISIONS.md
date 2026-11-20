# Decisions Made

## 2026-11-20: One pyproject.toml per service in a uv workspace, over a single root pyproject or fully independent projects
Each service deploys separately with different dependencies (pybaseball and pandas only in ingest), so per-service projects keep the Lambda images small and limit each service to the packages it needs. The workspace's single root lockfile stops versions drifting between services. The main trade-off is that all services must agree on shared package versions. I'll revisit if two services need conflicting versions of the same package, or if a service moves to its own repo.

## 2026-11-20: Split API and agent into separate Lambdas; API inside the VPC, agent outside
A Lambda in a VPC has no internet access without a NAT gateway (~$30/mo), and only the agent needs the internet, to call Claude. The split also means the database is never reachable from the public internet. I'll revisit if traffic grows enough that ECS Fargate is cheaper than Lambda.

## 2026-11-20: Claude Haiku over a larger model for development
This is definitely the cheapest option while the loop is being built; the eval suite will show whether a bigger model is needed. I'll revisit if q001–q010 accuracy stalls below the 90% target.

## 2026-11-20: Postgres over DuckDB
This matches what a front-office customer would run in production, and RDS gives managed backups as well as IAM integration. DuckDB would be simpler and fast enough at this data size. I'll revisit if RDS costs exceed the $10/mo budget.

## 2026-11-20: uv over pip + venv
It produces faster installs and has a lockfile by default, so the Docker build is reproducible. I'll revisit if a deploy tool doesn't support it.
