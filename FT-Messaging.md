# FTMessaging — Testing Readiness Report

## Table of Contents

- [1. Project Overview](#1-project-overview)
- [2. Testing Scope Inventory](#2-testing-scope-inventory)
  - [2.1 Module summary totals](#21-module-summary-totals)
  - [2.2 Complete file-by-file inventory](#22-complete-file-by-file-inventory-all-dart-files-under-lib)
    - [Controllers](#controllerschannel_management)
    - [Data](#data)
    - [Domain & use cases](#domain)
    - [Models](#models)
    - [Repositories](#repositories)
    - [Res & theme](#res)
    - [Services](#services)
    - [Plugin (`src`)](#src)
    - [Utils](#utils)
    - [Views](#viewschannel_management)
    - [Widgets](#widgets)
- [3. Tightly Coupled Code — Identified Blockers](#3-tightly-coupled-code--identified-blockers)
- [4. Refactoring Plan (Testing-Only, No Cleanup)](#4-refactoring-plan-testing-only-no-cleanup)
  - [Refactor 1 — API wrapper seams](#refactor-1--api-wrapper-seams)
  - [Refactor 2 — Utility static global split](#refactor-2--utility-static-global-split)
  - [Refactor 3 — Repository injection seam](#refactor-3--repository-injection-seam)
  - [Refactor 4 — Media use case platform adapters](#refactor-4--media-use-case-platform-adapters)
  - [Refactor 5 — Plugin runtime extraction](#refactor-5--plugin-runtime-extraction)
  - [Refactor 6 — MQTT manager client abstraction](#refactor-6--mqtt-manager-client-abstraction)
- [5. Refactoring Priority Order](#5-refactoring-priority-order)
- [6. Unit Testing Plan — Module by Module](#6-unit-testing-plan--module-by-module)
  - [6.1 Auth-first (host-auth integration layer)](#61-auth-first-host-auth-integration-layer)
  - [6.2 Domain use cases (high ROI)](#62-domain-use-cases-high-roi)
  - [6.3 Models](#63-models)
  - [6.4 Repositories](#64-repositories)
  - [6.5 Shared/state services](#65-sharedstate-services)
- [7. Integration & Widget Testing Opportunities](#7-integration--widget-testing-opportunities)
  - [Widget testing candidates](#widget-testing-candidates)
  - [Integration/E2E candidates](#integratione2e-candidates)
  - [Can run in parallel with unit testing](#can-run-in-parallel-with-unit-testing)
- [8. Definition of Done — Per File](#8-definition-of-done--per-file)
- [9. Progress Tracking Metrics](#9-progress-tracking-metrics)
- [10. Consistency Standards Across Developers](#10-consistency-standards-across-developers)
- [11. Approval & Review Process (Refactoring)](#11-approval--review-process-refactoring)
- [12. Real Examples from THIS Codebase](#12-real-examples-from-this-codebase)
  - [Example A — Static auth/token coupling](#example-a-static-authtoken-coupling-in-utility-headers)
  - [Example B — Repository hard-instantiation](#example-b-repository-hard-instantiation-of-api-wrapper)
  - [Example C — API wrapper side effects](#example-c-api-wrapper-side-effects-mixed-with-transport)
- [13. Recommended flutter_test Setup](#13-recommended-flutter_test-setup)
  - [`pubspec.yaml` additions](#pubspecyaml-additions-dev-dependencies)
  - [Project-specific setup](#project-specific-setup)
  - [Commands](#commands)
- [Final Summary Table](#final-summary-table)

---

## 1. Project Overview
- `ft_messaging` is a Flutter plugin/package that provides end-to-end messaging features: channels, groups, media attachments, rich text, unread states, profile updates, and MQTT-driven realtime events.
- Package metadata in `pubspec.yaml` confirms iOS/Android/Web support and GetX usage.
- Public package entry is `lib/ft_messaging.dart`, which exports `lib/src/ft_messaging_plugin.dart`.
- Host-app example entrypoint is `example/lib/main.dart`, where `FTMessaging.initialize(...)` is called before `runApp(...)`.
- Architecture pattern observed:
  - **GetX + layered MVVM-ish structure**
  - `views` (UI) -> `controllers` (state orchestration) -> `domain/use_cases` (business logic) -> `repositories` (data orchestration) -> `data/remote` and `data/local` services.
- Major modules/features discovered:
  - `channel_management`
  - `channel_messages`
  - `create_new_channel`
  - `create_new_group`
  - `home` (tabs, browse, global search, notification settings)
  - `profile`
  - `select_members`
  - `select_staff_members`
  - cross-cutting `repositories`, `domain/use_cases`, `services`, `utils`, `models`, and plugin bootstrap/wrappers.
- Auth note (important for planning order): there is **no dedicated `auth` module** in this package. Auth is supplied by host app through `UserDetails.authToken` in `FTMessaging.initialize(...)`.

---

## 2. Testing Scope Inventory

Classification legend:
- ✅ Unit Testable (no refactor needed)
- ⚠️ Needs Minor Refactor
- ❌ Needs Major Refactor
- ⏭️ Skip for Now

Recommended test type legend:
- Unit | Widget | Integration | None

### 2.1 Module summary totals

| Module | Files total | Testable now | Needs refactor | Skip |
|---|---:|---:|---:|---:|
| controllers/channel_management | 3 | 0 | 0 | 3 |
| controllers/channel_messages | 18 | 0 | 0 | 18 |
| controllers/controllers.dart | 1 | 0 | 0 | 1 |
| controllers/create_new_channel | 11 | 0 | 0 | 11 |
| controllers/create_new_group | 5 | 0 | 0 | 5 |
| controllers/home | 27 | 0 | 0 | 27 |
| controllers/profile | 3 | 0 | 0 | 3 |
| controllers/select_members | 3 | 0 | 0 | 3 |
| controllers/select_staff_members | 3 | 0 | 0 | 3 |
| data | 1 | 0 | 0 | 1 |
| data/local | 5 | 1 | 2 | 2 |
| data/remote | 3 | 1 | 1 | 1 |
| domain | 2 | 0 | 1 | 1 |
| domain/use_cases/base_use_case.dart | 1 | 1 | 0 | 0 |
| domain/use_cases/channels | 17 | 16 | 0 | 1 |
| domain/use_cases/common | 3 | 2 | 0 | 1 |
| domain/use_cases/create_channel | 3 | 2 | 0 | 1 |
| domain/use_cases/create_new_general_message | 4 | 3 | 0 | 1 |
| domain/use_cases/create_new_group | 5 | 4 | 0 | 1 |
| domain/use_cases/datetime | 5 | 4 | 0 | 1 |
| domain/use_cases/global_search | 3 | 2 | 0 | 1 |
| domain/use_cases/media | 8 | 1 | 6 | 1 |
| domain/use_cases/message | 12 | 11 | 0 | 1 |
| domain/use_cases/profile | 3 | 2 | 0 | 1 |
| domain/use_cases/select_member | 13 | 12 | 0 | 1 |
| domain/use_cases/select_staff_members | 6 | 5 | 0 | 1 |
| domain/use_cases/ui | 2 | 1 | 0 | 1 |
| domain/use_cases/use_cases.dart | 1 | 0 | 0 | 1 |
| lib (root) | 1 | 0 | 0 | 1 |
| models | 36 | 35 | 0 | 1 |
| repositories | 11 | 0 | 10 | 1 |
| res | 1 | 0 | 0 | 1 |
| res/constants | 5 | 0 | 0 | 5 |
| res/theme | 4 | 0 | 0 | 4 |
| services | 2 | 1 | 0 | 1 |
| services/mqtt_service | 4 | 0 | 3 | 1 |
| services/shared_services | 7 | 0 | 5 | 2 |
| services/state_services | 5 | 0 | 4 | 1 |
| src | 1 | 0 | 1 | 0 |
| utils | 10 | 6 | 3 | 1 |
| utils/config | 5 | 0 | 4 | 1 |
| utils/navigators | 4 | 1 | 2 | 1 |
| utils/translations | 4 | 3 | 0 | 1 |
| views/channel_management | 13 | 0 | 0 | 13 |
| views/channel_messages | 19 | 0 | 0 | 19 |
| views/create_new_channel | 37 | 0 | 0 | 37 |
| views/create_new_group | 17 | 0 | 0 | 17 |
| views/home | 36 | 0 | 0 | 36 |
| views/profile | 2 | 0 | 0 | 2 |
| views/select_members | 11 | 0 | 0 | 11 |
| views/select_staff_members | 7 | 0 | 0 | 7 |
| views/views.dart | 1 | 0 | 0 | 1 |
| widgets | 14 | 0 | 0 | 14 |

### 2.2 Complete file-by-file inventory (all Dart files under `lib/`)

> Scope policy used: all `views`, `widgets`, `controllers`, constants/theme, and barrel-only files are marked `⏭️ Skip for Now` for phase-1 unit readiness. Refactor statuses are focused on currently high-value logic/infrastructure files.

## controllers/channel_management
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_management/channel_management.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_management/channel_management_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_management/channel_management_controller.dart | ⏭️ Skip for Now | None

## controllers/channel_messages
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/channel_messages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/channel_messages_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/channel_messages_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/channel_messages_global_search_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/delete_msg_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_composition_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_draft_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_media_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_more_action_dialog_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_reaction_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/message_read_viewport_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/msg_read_unread_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/my_thread_msgs_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/thread_message_reaction_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/thread_notification_preferences_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/unread_channel_state_sync_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/channel_messages/mixins/unread_messages_dialog_mixin.dart | ⏭️ Skip for Now | None

## controllers/controllers.dart
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/controllers.dart | ⏭️ Skip for Now | None

## controllers/create_new_channel
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/create_channel_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/create_channel_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/create_new_channel.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/create_and_update_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/edit_channel_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/general_member_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/general_mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/invite_groups_for_channel_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/members_mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/staff_mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_channel/mixins/sync_mixins.dart | ⏭️ Skip for Now | None

## controllers/create_new_group
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_group/create_new_group.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_group/create_new_group_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_group/create_new_group_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_group/mixins/group_details_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/create_new_group/mixins/mixins.dart | ⏭️ Skip for Now | None

## controllers/home
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/home.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/home_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/home_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/mixins/mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/mixins/mqtt_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/mixins/mqtt_new_and_reply_msg_event_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/mixins/mqtt_reaction_and_reply_reaction_event_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/channels.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/channels_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/channels_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/draft_mutation_sync_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/favorite_unfavorite_channels_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/get_unread_threads_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/join_leave_channels_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/mixins.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/my_drafts_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/channels/mixins/unread_channels_mixin.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/global_search/global_search_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/global_search/global_search_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/global_search/search.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/groups/groups.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/groups/groups_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/groups/groups_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/notification_settings/notification_settings.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/notification_settings/notification_settings_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/notification_settings/notification_settings_controller.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/home/tab_item_controllers/tab_item_controllers.dart | ⏭️ Skip for Now | None

## controllers/profile
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/profile/profile.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/profile/profile_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/profile/profile_controller.dart | ⏭️ Skip for Now | None

## controllers/select_members
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_members/select_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_members/select_members_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_members/select_members_controller.dart | ⏭️ Skip for Now | None

## controllers/select_staff_members
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_staff_members/select_staff_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_staff_members/select_staff_members_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/controllers/select_staff_members/select_staff_members_controller.dart | ⏭️ Skip for Now | None

## data
- /Users/harshit/StudioProjects/FTMessaging/lib/data/data.dart | ⏭️ Skip for Now | None

## data/local
- /Users/harshit/StudioProjects/FTMessaging/lib/data/local/db_wrapper.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/data/local/local.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/data/local/managers/flutter_secure_storage_manager.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/data/local/managers/managers.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/data/local/managers/shared_preferences_manager.dart | ⚠️ Needs Minor Refactor | Unit

## data/remote
- /Users/harshit/StudioProjects/FTMessaging/lib/data/remote/api_wrapper.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/data/remote/apis.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/data/remote/remote.dart | ⏭️ Skip for Now | None

## domain
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/dependency_injection.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/domain.dart | ⏭️ Skip for Now | None

## domain/use_cases/base_use_case.dart
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/base_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/channels
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/browse_channels_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/channels.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/delete_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/delete_draft_message_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/delete_member_from_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/delete_staff_member_from_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/favorite_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/get_all_channels_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/get_draft_messages_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/get_messaging_permissions_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/get_my_channels_and_favorite_channels_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/get_unread_threads.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/join_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/leave_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/save_or_update_draft_message_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/unfavorite_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/channels/update_notification_preferences_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/common
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/common/common_use_case.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/common/get_groups_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/common/unread_summary_usecase.dart | ✅ Unit Testable | Unit

## domain/use_cases/create_channel
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_channel/create_channel.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_channel/create_channel_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_channel/get_channel_staff_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/create_new_general_message
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_general_message/create_new_general_message_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_general_message/get_activity_list_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_general_message/get_event_list_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_general_message/prepare_general_message_recipients_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/create_new_group
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_group/create_new_group.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_group/create_new_group_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_group/get_all_minimal_channels_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_group/update_group_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/create_new_group/validate_group_name_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/datetime
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/datetime/compare_dates_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/datetime/datetime_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/datetime/determine_date_header_visibility_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/datetime/format_datetime_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/datetime/get_day_with_suffix_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/global_search
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/global_search/build_highlighted_text_spans_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/global_search/global_search.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/global_search/global_search_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/media
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/generate_video_thumbnail_use_case.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/media_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/pick_document_use_case.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/pick_image_use_case.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/pick_profile_image_use_case.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/pick_video_use_case.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/read_file_bytes_io.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/media/read_file_bytes_stub.dart | ✅ Unit Testable | Unit

## domain/use_cases/message
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/add_or_remove_msg_reaction_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/delete_msg_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/format_message_html_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/get_channel_details_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/get_messages_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/mark_msg_as_read_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/message_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/process_media_in_message_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/remove_html_tags_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/send_message_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/strip_html_for_preview_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/message/validate_message_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/profile
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/profile/profile.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/profile/update_profile_details_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/profile/validate_user_name_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/select_member
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/fetch_members_by_type_filters_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/get_member_and_membership_types_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/get_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/member_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/merge_selected_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/prepare_member_filters_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/process_member_selection_after_fetch_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/search_selected_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/toggle_member_selection_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/update_member_sync_type_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/update_select_all_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/update_selected_member_types_for_sync_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_member/update_selected_membership_types_for_sync_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/select_staff_members
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/get_staff_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/merge_selected_staff_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/search_selected_staff_members_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/staff_member_use_cases.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/toggle_staff_member_selection_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/select_staff_members/update_select_all_staff_members_use_case.dart | ✅ Unit Testable | Unit

## domain/use_cases/ui
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/ui/calculate_tab_indicator_dimensions_use_case.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/ui/ui_use_cases.dart | ⏭️ Skip for Now | None

## domain/use_cases/use_cases.dart
- /Users/harshit/StudioProjects/FTMessaging/lib/domain/use_cases/use_cases.dart | ⏭️ Skip for Now | None

## lib (root)
- /Users/harshit/StudioProjects/FTMessaging/lib/ft_messaging.dart | ⏭️ Skip for Now | None

## models
- /Users/harshit/StudioProjects/FTMessaging/lib/models/channels_response_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/create_channel_payload_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/create_group_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/create_group_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/create_new_channel_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/create_new_group_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/delete_channel_response_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_activity_list_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_all_minimal_channles_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_channel_staff_response.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_draft_messages_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_event_list_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_group_details_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_group_labels_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_groups_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_member_and_membership_type_response.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_members_response.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_messages_response_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/get_staff_members_response.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/global_search_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/group_management_data_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/messaging_permissions_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/models.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/models/mqtt_event_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/msg_reaction_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/notification_settings_item_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/parsed_draft_key_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/response_models.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/save_draft_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/send_message_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/sync_condition_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/unread_group_messages_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/unread_summary_res_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/unread_threads_response_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/update_notification_preferences_model.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/models/upload_media_model.dart | ✅ Unit Testable | Unit

## repositories
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/channel_messages_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/channels_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/common_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/create_channel_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/create_new_general_message_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/create_new_group_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/global_search_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/groups_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/home_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/profile_repository.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/repositories/repositories.dart | ⏭️ Skip for Now | None

## res
- /Users/harshit/StudioProjects/FTMessaging/lib/res/res.dart | ⏭️ Skip for Now | None

## res/constants
- /Users/harshit/StudioProjects/FTMessaging/lib/res/constants/app_constants.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/constants/asset_constants.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/constants/config_constants.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/constants/constants.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/constants/string_contants.dart | ⏭️ Skip for Now | None

## res/theme
- /Users/harshit/StudioProjects/FTMessaging/lib/res/theme/colors.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/theme/dimens.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/theme/styles.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/res/theme/theme.dart | ⏭️ Skip for Now | None

## services
- /Users/harshit/StudioProjects/FTMessaging/lib/services/media_service.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/services/services.dart | ⏭️ Skip for Now | None

## services/mqtt_service
- /Users/harshit/StudioProjects/FTMessaging/lib/services/mqtt_service/mqtt_client_platform_io.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/mqtt_service/mqtt_client_platform_web.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/mqtt_service/mqtt_service.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/services/mqtt_service/mqtt_service_manager.dart | ❌ Needs Major Refactor | Integration

## services/shared_services
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_channel_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_member_selection_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_member_sync_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_messaging_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_services.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_services_binding.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/services/shared_services/shared_staff_member_selection_service.dart | ⚠️ Needs Minor Refactor | Integration

## services/state_services
- /Users/harshit/StudioProjects/FTMessaging/lib/services/state_services/channel_state_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/state_services/general_message_state_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/state_services/member_selection_state_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/state_services/message_state_service.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/services/state_services/state_services.dart | ⏭️ Skip for Now | None

## src
- /Users/harshit/StudioProjects/FTMessaging/lib/src/ft_messaging_plugin.dart | ❌ Needs Major Refactor | Integration

## utils
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/debouncer.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/enums.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/extensions.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/ft_messaging_getx_wrapper.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/ft_messaging_non_getx_wrapper.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/input_formatters.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/log.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/single_loader.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/utility.dart | ❌ Needs Major Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/utils.dart | ⏭️ Skip for Now | None

## utils/config
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/config/app_config.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/config/communication_config.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/config/config.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/config/device_config.dart | ⚠️ Needs Minor Refactor | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/config/env_config.dart | ⚠️ Needs Minor Refactor | Unit

## utils/navigators
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/navigators/app_pages.dart | ⚠️ Needs Minor Refactor | Integration
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/navigators/app_routes.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/navigators/navigators.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/navigators/routes_management.dart | ⚠️ Needs Minor Refactor | Integration

## utils/translations
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/translations/translation_en.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/translations/translation_keys.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/translations/translation_values.dart | ✅ Unit Testable | Unit
- /Users/harshit/StudioProjects/FTMessaging/lib/utils/translations/translations.dart | ⏭️ Skip for Now | None

## views/channel_management
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/channel_management.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/channel_management_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/pages/pages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/channel_button.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/channel_loading_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/empty_channel_list_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/expandable_channel_card_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/filter_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/header_controls_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/mobile_expandable_channel_card_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/search_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/web_expandable_channel_card_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_management/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/channel_messages
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/channel_messages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/channel_messages_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/pages/enter_message_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/pages/my_thread_msgs_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/pages/pages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/channel_messages_list_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/custom_html_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/delete_message_confirmation_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/message_action_bar.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/message_options_popover.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/message_reaction_picker.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/message_thread_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/reaction_details_bottom_sheet_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/scroll_to_load_new_messages_hint.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/single_message_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/single_thread_message_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/unread_messages_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/unread_messages_page_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/channel_messages/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/create_new_channel
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/create_channel_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/create_new_channel.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/pages/general_settings.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/pages/members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/pages/pages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/pages/staff_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/pages/sync_settings.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/add_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/add_staff_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/basic_infomation.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/captain_search.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/caption_configuration.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/channel_type_settings.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/condition_sync_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/create_channel_tabs.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/criteria_selection_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/current_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/current_staff_members_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/failed_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/invite_group_button.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/invite_groups_confirm_summary.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/invite_groups_dialog_body.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/invite_groups_dialog_skeleton.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/invite_groups_for_channel_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/loading_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/member_card_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/member_warning.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/permitted_member.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/selected_staff.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/single_invite_group_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/staff_card_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/staff_conditions.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/staff_group_bottom.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/staff_messagin_access.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/staff_permission.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/sync_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_channel/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/create_new_group
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/create_new_group.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/create_new_group_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/pages/group_details_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/pages/pages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/add_staff_members_section.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/group_details_staff_members_section.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/group_details_staff_search_with_dropdown.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channel_row.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_channel_list.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_drag_handle.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_empty_state.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_footer.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_header.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/select_channels_bottom_sheet_selectable_channel_row.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/staff_member_row.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/create_new_group/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/home
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/home.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/home_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/browse_channels_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/global_search_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/groups_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/home_tab_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/notification_settings_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/pages/pages.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/channel_more_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/custom_radio_button.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/custom_switch_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/delete_channel_confirmation_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/delete_draft_confirmation_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/favorite_channels_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/home_tab_bar_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/home_tab_section_header.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/home_tab_section_header_skeleton.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/leave_channel_confirmation_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/my_channels_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/my_drafts_section.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/my_threads_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/recipients_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/search_channels_text_field.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/search_members_text_field.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_channel_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_global_search_result_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_group_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_member_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_my_draft_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_my_threads_channel_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/single_recent_message_item.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/unread_messages_banner.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/unread_messages_banner_skeleton.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/view_members_and_staff_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/view_uploaded_media.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/home/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/profile
- /Users/harshit/StudioProjects/FTMessaging/lib/views/profile/my_profile_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/profile/profile.dart | ⏭️ Skip for Now | None

## views/select_members
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/select_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/select_members_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/custom_membership_and_member_type_dropdown.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/member_filters_bottom_sheet_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/member_selection_type_switcher.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/member_sync_options.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/members_list_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/select_members_filters.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/select_members_for_tablet.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/selected_members_panel.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_members/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/select_staff_members
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/select_staff_members.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/select_staff_members_view.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/widgets/section_text_widgets.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/widgets/select_staff_members_for_tablet.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/widgets/select_staff_members_table_header.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/widgets/select_staff_members_table_row.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/views/select_staff_members/widgets/widgets.dart | ⏭️ Skip for Now | None

## views/views.dart
- /Users/harshit/StudioProjects/FTMessaging/lib/views/views.dart | ⏭️ Skip for Now | None

## widgets
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_app_bar.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_button.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_drop_down.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_html_editor.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_switch_tile.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/custom_text_field.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/emoji_picker_widget.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/insert_link_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/loader.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/mqtt_connection_indicator.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/responsive_layout.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/user_avatar.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/user_details_dialog_content.dart | ⏭️ Skip for Now | None
- /Users/harshit/StudioProjects/FTMessaging/lib/widgets/widgets.dart | ⏭️ Skip for Now | None

---

## 3. Tightly Coupled Code — Identified Blockers

| File | What test CANNOT be written today? | What dependency/coupling is blocking it? | Smallest refactor to unblock testing | Risk this refactor introduces | Suggested Priority |
|------|--------------------------------------|------------------------------------------|--------------------------------------|-------------------------------|-------------------|
| `lib/data/remote/api_wrapper.dart` | deterministic unit tests for request/response branches without Flutter/Get context | direct `http` static calls, `Get.context`, `SingleLoader`, `Utility.showInfoDialog` side effects | inject `HttpClientAdapter`, `LoaderPort`, `DialogPort`, `NetworkChecker` into constructor | medium: runtime API behavior regression if adapter wiring is wrong | P0 |
| `lib/utils/utility.dart` | isolated unit tests for dialogs/snackbars/network checks | static methods with `Get.context`, `showDialog`, `Connectivity()` direct call | split into ports (`ConnectivityPort`, `DialogPort`, `SnackbarPort`), keep static facade delegating to injectable instance | medium: global behavior changes if singleton not initialized | P0 |
| `lib/domain/dependency_injection.dart` | deterministic tests for registration graph and reset behavior | hardcoded global `Get.put`/`Get.lazyPut` with concrete classes | create `DependencyRegistrar` interface and pass registrar in tests | low-medium: startup registration order issues | P1 |
| `lib/src/ft_messaging_plugin.dart` | unit test for initialize/open flows without Flutter nav and Get globals | static mutable singleton + direct `Navigator` + direct `Get.put` calls | extract `FTMessagingRuntime` injectable service; keep static wrapper forwarding calls | high: plugin API lifecycle regressions | P0 |
| `lib/utils/ft_messaging_getx_wrapper.dart` | deterministic back-nav behavior unit tests | relies on global `Get` navigation state | inject `NavigationStateReader` abstraction | low: wrapper back behavior edge cases | P2 |
| `lib/utils/ft_messaging_non_getx_wrapper.dart` | deterministic tests for pop logic without real Get stack | `getx.Get.*` static state + `GetMaterialApp` internals | isolate `_canPop` and `_handleBackNavigation` into pure helper with state DTO | low | P2 |
| `lib/services/mqtt_service/mqtt_service_manager.dart` | unit tests for reconnect/subscription logic | concrete platform client + singleton style manager state | constructor inject `MqttClientPlatform` and clock/timer abstractions | medium-high: realtime reconnect behavior | P1 |
| `lib/services/mqtt_service/mqtt_client_platform_io.dart` | unit tests for connect/publish/subscribe without real broker | concrete `mqtt_client` socket dependency | extract interface + fakeable transport adapter | low-medium | P2 |
| `lib/services/mqtt_service/mqtt_client_platform_web.dart` | same as above for web | browser websocket/mqtt concrete dependency | same adapter strategy as IO client | low-medium | P2 |
| `lib/repositories/common_repository.dart` | pure unit tests for URL/header behavior with mock responses | internally constructs `FtMessagingApiWrapper` + static `FTMessaging.clubId`/`Utility.commonHeaders` | constructor inject `FtMessagingApiWrapper` and `AuthContextProvider` | low | P1 |
| `lib/repositories/home_repository.dart` | unit test for auth header fallback and failure paths | reads `FTMessaging.authToken` static | inject `TokenProvider`/headers builder | low | P1 |
| `lib/repositories/channels_repository.dart` | full unit isolation of request arguments | direct wrapper construction and utility/header statics | inject wrapper and headers provider | low | P2 |
| `lib/repositories/channel_messages_repository.dart` | same as above | same coupling pattern | inject wrapper and remove static lookups from methods | low | P2 |
| `lib/repositories/create_channel_repository.dart` | same as above | same | same | low | P2 |
| `lib/repositories/create_new_general_message_repository.dart` | same as above | same | same | low | P2 |
| `lib/repositories/create_new_group_repository.dart` | same as above | same | same | low | P2 |
| `lib/repositories/global_search_repository.dart` | same as above | same | same | low | P2 |
| `lib/repositories/groups_repository.dart` | same as above | same | same | low | P2 |
| `lib/repositories/profile_repository.dart` | same as above | same | same | low | P2 |
| `lib/domain/use_cases/media/pick_image_use_case.dart` | platform-independent unit tests for media picking behavior | direct `ImagePicker` and `File`/web APIs in use case | inject `ImagePickerPort` and `FileBytesReader` | medium | P1 |
| `lib/domain/use_cases/media/pick_video_use_case.dart` | same | same | same | medium | P1 |
| `lib/domain/use_cases/media/pick_document_use_case.dart` | same | direct file picker platform APIs | inject picker port | medium | P1 |
| `lib/domain/use_cases/media/pick_profile_image_use_case.dart` | same | direct picker and compression stack | inject picker/compressor ports | medium | P1 |
| `lib/domain/use_cases/media/generate_video_thumbnail_use_case.dart` | deterministic tests for thumbnail generation result mapping | direct plugin invocation | inject thumbnail generator port | low-medium | P2 |
| `lib/domain/use_cases/media/read_file_bytes_io.dart` | pure tests without filesystem | direct `dart:io` file calls | inject file reader adapter | low | P3 |
| `lib/data/local/managers/flutter_secure_storage_manager.dart` | unit tests without platform channel | direct `FlutterSecureStorage` construction | inject storage interface | low | P2 |
| `lib/data/local/managers/shared_preferences_manager.dart` | unit tests without real prefs | singleton access and static plugin API | inject preferences adapter | low | P2 |
| `lib/utils/config/app_config.dart` | unit tests without global init ordering | static global mutable config | allow instance-based config object for tests | low | P3 |
| `lib/utils/config/communication_config.dart` | deterministic behavior tests across envs | static constants/state from global config | pass config object explicitly | low | P3 |
| `lib/utils/config/device_config.dart` | tests for platform metadata behavior | direct device/platform plugin calls | inject device info provider | low | P3 |
| `lib/utils/config/env_config.dart` | tests without env file dependency | direct env lookups | inject env source map | low | P3 |
| `lib/utils/navigators/app_pages.dart` | unit tests for route registry without Get internals | static GetPage globals tightly coupled | expose route factory returning pure metadata list | low | P3 |
| `lib/utils/navigators/routes_management.dart` | unit tests for navigation decisions | direct `Get.toNamed` static calls | inject `RouteNavigator` interface | low | P3 |
| `lib/services/shared_services/shared_channel_service.dart` | unit tests for service behavior without global Get state | hidden `Get.find` dependencies and mutable singleton style state | constructor inject use cases/services | low-medium | P2 |
| `lib/services/shared_services/shared_member_selection_service.dart` | same | same | same | low-medium | P2 |
| `lib/services/shared_services/shared_member_sync_service.dart` | same | same | same | low-medium | P2 |
| `lib/services/shared_services/shared_messaging_service.dart` | same | same | same | low-medium | P2 |
| `lib/services/shared_services/shared_staff_member_selection_service.dart` | same | same | same | low-medium | P2 |
| `lib/services/state_services/channel_state_service.dart` | deterministic unit tests with clean state setup | implicit global lifecycle and mutable singleton | add reset hooks + constructor defaults injection | low | P3 |
| `lib/services/state_services/general_message_state_service.dart` | same | same | same | low | P3 |
| `lib/services/state_services/member_selection_state_service.dart` | same | same | same | low | P3 |
| `lib/services/state_services/message_state_service.dart` | same | same | same | low | P3 |

---

## 4. Refactoring Plan (Testing-Only, No Cleanup)

### Refactor 1 — API wrapper seams
- **File:** `lib/data/remote/api_wrapper.dart`
- **Current problem:** hardcoded side-effects and transport make it non-deterministic in unit tests.
- **Example current pattern:**

```37:43:lib/data/remote/api_wrapper.dart
if (showLoader) {
  final context = Get.overlayContext ?? Get.context;
  if (context != null) {
    SingleLoader.instance.show(context);
  }
}
```

- **Exact change needed:** introduce constructor params:
  - `HttpClientAdapter httpClient`
  - `NetworkChecker networkChecker`
  - `LoaderPort loader`
  - `DialogPort dialog`
- **After shape (example):**
```dart
class FtMessagingApiWrapper {
  FtMessagingApiWrapper({
    required this.httpClient,
    required this.networkChecker,
    required this.loader,
    required this.dialog,
  });
}
```
- **Estimated effort:** Medium (2-4hr)

### Refactor 2 — Utility static global split
- **File:** `lib/utils/utility.dart`
- **Current problem:** static methods mix pure helpers with UI/runtime side effects and direct Get access.
- **Example current pattern:**

```543:548:lib/utils/utility.dart
static Map<String, String> commonHeaders() => {
  'Content-Type': 'application/json',
  'authorization': FTMessaging.authToken ?? '',
  'lan': 'en',
};
```

- **Exact change needed:** move side-effectful methods to `UtilityUiService` (injectable), keep pure helpers (`getUserInitials`, date formatting helpers) static/pure.
- **Estimated effort:** Medium (2-4hr)

### Refactor 3 — Repository injection seam
- **Files:** all repository files under `lib/repositories/` except `repositories.dart`
- **Current problem:** repositories instantiate `FtMessagingApiWrapper` internally and pull auth/club info from statics.
- **Example current pattern:**

```13:21:lib/repositories/common_repository.dart
final _apiWrapper = FtMessagingApiWrapper();
...
'${Apis.memberShipAndType}?clubId=${FTMessaging.clubId}',
```

- **Exact change needed:** constructor-inject wrapper and auth/context providers.
- **Estimated effort:** Small per file (<1hr), Medium for all repos (2-4hr)

### Refactor 4 — Media use case platform adapters
- **Files:** media picker/generation/readers under `lib/domain/use_cases/media/`
- **Current problem:** direct platform plugin usage blocks pure unit tests.
- **Exact change needed:** abstract picker/file/thumbnail plugins behind ports; use fakes in tests.
- **Estimated effort:** Medium (2-4hr)

### Refactor 5 — Plugin runtime extraction
- **File:** `lib/src/ft_messaging_plugin.dart`
- **Current problem:** static singleton controls init, navigation, DI, and runtime state.
- **Exact change needed:** keep public static API but delegate to injectable runtime object.
- **Estimated effort:** Large (1+ day)

### Refactor 6 — MQTT manager client abstraction
- **Files:** `lib/services/mqtt_service/*` except barrel.
- **Current problem:** direct broker/platform coupling and reconnect state difficult to fake.
- **Exact change needed:** adapter interface + clock/timer abstraction.
- **Estimated effort:** Large (1+ day)

---

## 5. Refactoring Priority Order

> Team rule says start with Auth module. Since no dedicated Auth module exists, priority starts with auth-adjacent integration points.

1. `lib/utils/utility.dart` — carries auth header generation via `FTMessaging.authToken` and many side effects used across stack.
2. `lib/data/remote/api_wrapper.dart` — central network execution, errors, loader/dialog side effects.
3. `lib/repositories/home_repository.dart` — direct auth-header dependency and high app-surface impact.
4. `lib/repositories/common_repository.dart` — shared member/channel endpoints used broadly.
5. remaining repository files — highest test value at low refactor risk.
6. media use cases in `lib/domain/use_cases/media/` — isolates platform/plugin dependencies.
7. `lib/domain/dependency_injection.dart` — improves test harness setup.
8. `lib/src/ft_messaging_plugin.dart` — high-impact but higher risk.
9. MQTT service files — important but can proceed after core unit seam creation.
10. config/navigation shared helpers — lower direct business-risk, can follow.

---

## 6. Unit Testing Plan — Module by Module

### 6.1 Auth-first (host-auth integration layer)
- **Module reality:** no `auth/`; use these files first:
  - `lib/utils/utility.dart`
  - `lib/data/remote/api_wrapper.dart`
  - `lib/repositories/home_repository.dart`
  - `lib/repositories/common_repository.dart`
- **Key tests:**
  - `Utility.commonHeaders()`
    - input: token present / empty
    - expected: header map includes `authorization`, `lan`, `Content-Type`
    - edge: null token fallback
    - mocks: token provider (after refactor)
  - `FtMessagingApiWrapper.makeRequest(...)`
    - input: timeout, no-network, 200/400/500 responses
    - expected: `ResponseModel` mapping and error handling
    - edge: recursive 406 retry branch
    - mocks: fake http adapter, fake network checker, fake dialog/loader
  - `FtMessagingHomeRepository.getMessagingPermissions(...)`
    - input: valid response JSON / parse failure / API error
    - expected: `Success(MessagingPermissionsResModel)` or `Failure(...)`
    - mocks: api wrapper stub

### 6.2 Domain use cases (high ROI)
- Target all non-platform use cases in:
  - `lib/domain/use_cases/channels/`
  - `lib/domain/use_cases/message/`
  - `lib/domain/use_cases/select_member/`
  - `lib/domain/use_cases/select_staff_members/`
  - `lib/domain/use_cases/datetime/`
  - `lib/domain/use_cases/global_search/`
  - `lib/domain/use_cases/profile/`
- Typical pattern:
  - pass params DTO
  - assert success/failure result object
  - verify repository method invoked with expected arguments.
- Mocks: repository fakes only.

### 6.3 Models
- Target all model files in `lib/models/` except barrel.
- Tests:
  - `fromJson`/`fromRawJson` happy path
  - missing keys/null fields
  - malformed types
  - round-trip serialization (if `toJson` available).

### 6.4 Repositories
- After constructor injection refactor, for each repository:
  - verify URL/query/body construction
  - verify header construction paths
  - verify parse success/failure handling
  - verify failure message mapping.
- Mocks: `FtMessagingApiWrapper` fake and token/context provider fake.

### 6.5 Shared/state services
- Validate state transitions and reset semantics.
- Mocks: use case fakes for services that orchestrate API calls.

---

## 7. Integration & Widget Testing Opportunities

### Widget testing candidates
- `lib/views/home/home_view.dart` (tab rendering, role-dependent tabs)
- `lib/views/channel_messages/channel_messages_view.dart` (message list render and action bar states)
- `lib/views/create_new_channel/create_channel_view.dart` (multi-step form progression)
- `lib/views/select_members/select_members_view.dart` and `lib/views/select_staff_members/select_staff_members_view.dart` (selection interactions)

### Integration/E2E candidates
- Plugin init -> open messaging -> back navigation via wrappers:
  - `lib/src/ft_messaging_plugin.dart`
  - `lib/utils/ft_messaging_getx_wrapper.dart`
  - `lib/utils/ft_messaging_non_getx_wrapper.dart`
- Realtime flow smoke:
  - open channel -> receive MQTT event -> UI updates (`home` + `channel_messages`)
- Compose/send flow:
  - create message with attachment -> send -> appears in list.

### Can run in parallel with unit testing
- Widget tests for pure UI render of static widgets and forms.
- Integration smoke for wrapper navigation and basic route wiring.
- These do not need to wait for full repository/use-case unit test completion.

---

## 8. Definition of Done — Per File

Applied per tested file (not skipped files):

| File category | Min coverage target | Happy paths | Error/edge cases | CI passing | Peer reviewed |
|---|---|---|---|---|---|
| Use cases/models | >= 90% lines/branches | Yes | Yes | Yes | Yes |
| Repositories | >= 85% (focus on behavior) | Yes | Yes | Yes | Yes |
| Shared/state services | >= 80% | Yes | Yes | Yes | Yes |
| Integration-heavy files (`api_wrapper`, wrappers, plugin bootstrap, MQTT) | Coverage not sole KPI; scenario checklist required | Yes | Yes | Yes | Yes |

Per-file Done checklist:
- tests added in correct `test/` path
- deterministic (no real network/platform side effects)
- all assertions explicit
- no flakiness after 5 local reruns
- included in CI test workflow.

---

## 9. Progress Tracking Metrics

For each completed file entry in shared tracking doc:
- file path
- classification at start (`✅/⚠️/❌/⏭️`)
- refactor needed? yes/no
- refactor PR/link (if any)
- test PR/link
- test counts added (`unit/widget/integration`)
- pass/fail status in CI
- reviewer + date

Report refactor and tests separately:
- `Refactor Done`: count of files moved from `⚠️/❌` to `✅`
- `Testing Done`: count of files with merged tests + passing CI

Weekly numbers:
- total files covered this week
- cumulative % of testable-now files completed
- cumulative refactor backlog burn-down
- flaky test count
- average PR cycle time for test PRs.

---

## 10. Consistency Standards Across Developers

- Test folder structure:
  - `test/unit/...` mirrors `lib/domain`, `lib/models`, `lib/repositories`
  - `test/widget/...` mirrors `lib/views` and reusable `lib/widgets`
  - `integration_test/...` for user journeys
- Naming:
  - file: `<source_file_name>_test.dart`
  - group: `<ClassName>.<method>`
  - case: `given_<condition>_when_<action>_then_<expectation>`
- Test case style:
  - Given / When / Then in comments or naming.
- Every test file header should include:
  - source file path
  - test owner
  - date
  - dependencies/mocks used
- Shared mocks/utilities:
  - `test/helpers/` for fakes/builders
  - `test/mocks/` for generated/manual mocks
  - never duplicate fake adapters across modules.

---

## 11. Approval & Review Process (Refactoring)

Use this template before starting any refactor:

```markdown
### Refactor Approval Request

1. File:
2. Test that cannot be written today:
3. Blocking dependency/coupling:
4. Smallest change to unblock test:
5. Regression risk:
6. Rollback plan:
7. Reviewer:
8. Approval date:
9. Related test case IDs:
```

---

## 12. Real Examples from THIS Codebase

### Example A: Static auth/token coupling in utility headers

Current coupling:

```543:548:lib/utils/utility.dart
static Map<String, String> commonHeaders() => {
  'Content-Type': 'application/json',
  'authorization': FTMessaging.authToken ?? '',
  'lan': 'en',
};
```

Refactored shape:
```dart
class HeaderBuilder {
  HeaderBuilder(this.tokenProvider);
  final String? Function() tokenProvider;
  Map<String, String> commonHeaders() => {
    'Content-Type': 'application/json',
    'authorization': tokenProvider() ?? '',
    'lan': 'en',
  };
}
```

New unit test made possible:
```dart
test('given token provider returns null when headers built then authorization is empty', () {
  final builder = HeaderBuilder(() => null);
  final headers = builder.commonHeaders();
  expect(headers['authorization'], '');
});
```

### Example B: Repository hard-instantiation of API wrapper

Current coupling:

```13:21:lib/repositories/common_repository.dart
final _apiWrapper = FtMessagingApiWrapper();
...
'${Apis.memberShipAndType}?clubId=${FTMessaging.clubId}',
```

Refactored shape:
```dart
class CommonRepository {
  CommonRepository(this.api, this.clubIdProvider, this.headerBuilder);
  final FtMessagingApiWrapper api;
  final String? Function() clubIdProvider;
  final Map<String, String> Function() headerBuilder;
}
```

New unit test made possible:
```dart
test('builds URL with provided club id', () async {
  final fakeApi = FakeApiWrapper();
  final repo = CommonRepository(fakeApi, () => 'club-1', () => {'Content-Type': 'application/json'});
  await repo.getMemberAndMembershipType();
  expect(fakeApi.lastApi, contains('clubId=club-1'));
});
```

### Example C: API wrapper side effects mixed with transport

Current coupling:

```37:43:lib/data/remote/api_wrapper.dart
if (showLoader) {
  final context = Get.overlayContext ?? Get.context;
  if (context != null) {
    SingleLoader.instance.show(context);
  }
}
```

Refactored shape:
```dart
class FtMessagingApiWrapper {
  FtMessagingApiWrapper({
    required this.httpClient,
    required this.loader,
  });
  final HttpClientAdapter httpClient;
  final LoaderPort loader;
}
```

New unit test made possible:
```dart
test('shows loader once and hides after success', () async {
  final loader = FakeLoader();
  final http = FakeHttpClient.ok('{}');
  final wrapper = FtMessagingApiWrapper(httpClient: http, loader: loader, ...);
  await wrapper.makeRequest('https://x', type: RequestType.get, headers: {}, showLoader: true);
  expect(loader.showCount, 1);
  expect(loader.hideCount, 1);
});
```

---

## 13. Recommended flutter_test Setup

### `pubspec.yaml` additions (dev dependencies)
- `flutter_test` (already present)
- `mocktail` (or `mockito`)
- `build_runner` (if using code-gen mocks)
- optional: `integration_test`

Suggested block:
```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  mocktail: any
  build_runner: any
  integration_test:
    sdk: flutter
```

### Project-specific setup
- create fake adapters for:
  - API transport
  - token provider/header builder
  - connectivity checker
  - media picker/file reader
  - mqtt client
- optional if using Mockito:
  - add `@GenerateMocks([...])` in test files and run build runner.

### Commands
- Unit tests:
  - `flutter test test/unit`
- Widget tests:
  - `flutter test test/widget`
- Integration tests:
  - `flutter test integration_test`
- Coverage:
  - `flutter test --coverage`

---

## Final Summary Table

| Module | Files Total | Testable Now | Needs Refactor | Skip | Estimated Effort |
|--------|-------------|--------------|----------------|------|-----------------|
| Controllers | 74 | 0 | 0 | 74 | N/A (deferred) |
| Views + Widgets | 157 | 0 | 0 | 157 | N/A (deferred) |
| Domain Use Cases | 87 | 79 | 6 | 2 | Medium |
| Models | 36 | 35 | 0 | 1 | Small |
| Repositories | 11 | 0 | 10 | 1 | Medium |
| Data (remote/local) | 8 | 2 | 3 | 3 | Medium |
| Services (shared/state/mqtt) | 18 | 1 | 12 | 5 | Large |
| Utils + Navigators + Config | 23 | 10 | 9 | 4 | Medium |
| Plugin Bootstrap (`src`) | 1 | 0 | 1 | 0 | Large |
| **Total** | **428** | **127** | **42** | **259** | **Phased: 3-5 sprints** |
