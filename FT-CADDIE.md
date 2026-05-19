# Foretees Caddie — Testing Readiness Report

## Index

1. [Project Overview](#1-project-overview)
2. [Testing Scope Inventory](#2-testing-scope-inventory)
3. [Tightly Coupled Code — Identified Blockers](#3-tightly-coupled-code--identified-blockers)
4. [Refactoring Plan (Testing-Only, No Cleanup)](#4-refactoring-plan-testing-only-no-cleanup)
5. [Refactoring Priority Order](#5-refactoring-priority-order)
6. [Unit Testing Plan — Module by Module](#6-unit-testing-plan--module-by-module)
7. [Integration & Widget Testing Opportunities](#7-integration--widget-testing-opportunities)
8. [Definition of Done — Per File](#8-definition-of-done--per-file)
9. [Progress Tracking Metrics](#9-progress-tracking-metrics)
10. [Consistency Standards Across Developers](#10-consistency-standards-across-developers)
11. [Approval & Review Process (Refactoring)](#11-approval--review-process-refactoring)
12. [Real Examples from THIS Codebase](#12-real-examples-from-this-codebase)
13. [Recommended `flutter_test` Setup](#13-recommended-flutter_test-setup)
14. [Summary Table](#summary-table)

## 1. Project Overview

Foretees Caddie is a Flutter application for caddies to log in to a club, manage schedule availability, confirm/decline/check in for loop assignments, read feed/rules content, update profile and notification preferences, contact the club, view loop history, and manage area/region assignments.

**Entry point files**

- `lib/main.dart` starts the production app as `ForeCaddie` with `flavoredApp = 1`.
- `lib/main_dev.dart` starts the dev app as `ForeCaddie Dev` with `flavoredApp = 2`.
- `lib/app.dart` initializes Flutter, Firebase, GetX services, Hive storage, route locator, `SystemChrome`, and `MyApp`.
- `lib/app/app.dart` is an app-layer barrel export.

**Architecture**

The project uses **GetX MVC/MVP-style layering**:

- Views/widgets use `GetBuilder`, `Get.find`, `Get.dialog`, `Get.arguments`, and named routes.
- Controllers hold UI state and call presenters.
- Presenters delegate to domain use cases.
- Use cases delegate to `Repository`.
- `Repository` coordinates `DeviceRepository` and `DataRepository`.
- `DataRepository` delegates network work to `ConnectHelper`, which delegates HTTP calls to `ApiWrapper`.
- `DeviceRepository` handles Hive/secure local storage.

The codebase also uses `get_it` for the dynamic routing `NavigationService`, but the dominant dependency pattern is GetX service location.

**Major modules/features**

- App bootstrap and navigation
- Auth/Login and club login
- Splash
- Home shell
- Feed
- Profile
- Edit Profile
- Schedule UI / availability / assignments
- Contacts
- Rules / agreements
- History
- Area Assignments
- Region Preferences
- Shared widgets
- Utilities and media management
- Domain use cases, entities, services
- Data/network layer
- Device/local storage layer

Existing tests live under `test/`. There is **no `integration_test/` directory** today.

## 2. Testing Scope Inventory

Legend:

- ✅ Unit Testable: no refactor needed.
- ⚠️ Needs Minor Refactor: small seam needed, or only part of the file is testable.
- ❌ Needs Major Refactor: tightly coupled to globals/platform/UI side effects.
- ⏭️ Skip for Now: UI-only barrel/static/generated/constants/DI route table.

### Auth / Login

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/login/login_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/login/login_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/login/login_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/login/login_view.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/login/club_login_view.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/login/widgets/email_with_login_send_dialog.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/login/widgets/first_agree_rules_view.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/login/login.dart` | ⏭️ Skip for Now | None |

### Splash

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/splash/splash_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/splash/splash_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/splash/splash_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/splash/splash_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/splash/splash.dart` | ⏭️ Skip for Now | None |

### Schedule UI

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/schedule_ui/schedule_ui_controller.dart` | ❌ Needs Major Refactor | Unit + Integration |
| `lib/app/pages/schedule_ui/schedule_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/schedule_ui/schedule_ui_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/schedule_ui/schedule_ui_view.dart` | ❌ Needs Major Refactor | Widget + Integration |
| `lib/app/pages/schedule_ui/schedule_ui.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/schedule_ui/widgets/available_btn_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/schedule_ui/widgets/button_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/schedule_ui/widgets/confirm_show_dialog.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/schedule_ui/widgets/decline_show_dialog.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/schedule_ui/widgets/example_dialog.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/schedule_ui/widgets/schedule_assignment_card.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/schedule_ui/widgets/schedule_day_date.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/schedule_ui/widgets/schedule_day_tea_time_sliders.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/schedule_ui/widgets/schedule_not_assigned_row.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/schedule_ui/widgets/schedule_ui_list_widget.dart` | ❌ Needs Major Refactor | Widget |

### Home / Feed / Profile

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/home/home_controller.dart` | ❌ Needs Major Refactor | Unit + Integration |
| `lib/app/pages/home/home_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/home_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/home_view.dart` | ❌ Needs Major Refactor | Integration |
| `lib/app/pages/home/home.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/pages/pages.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/pages/feed/feed_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/home/pages/feed/feed_view.dart` | ❌ Needs Major Refactor | Integration |
| `lib/app/pages/home/pages/feed/feed.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/pages/feed/view_btn_pdf_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/feed/widgets/attachement_widget.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/feed/widgets/complain_widget.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/feed/widgets/elevated_button_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/pages/feed/widgets/loop_assigned_message_span.dart` | ✅ Unit Testable | Unit/Widget |
| `lib/app/pages/home/pages/feed/widgets/message_board.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/pages/feed/widgets/news_feed_list.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/feed/widgets/pop_up_menu_widget.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/profile/profile_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/profile/profile.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/pages/profile/widgets/profile_header.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/profile/widgets/profile_image.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/pages/profile/widgets/profile_section.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/pages/profile/widgets/profile_tiles_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/models/model.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/home/models/profile_item_model.dart` | ✅ Unit Testable | Unit |
| `lib/app/pages/home/models/profile_section_model.dart` | ✅ Unit Testable | Unit |
| `lib/app/pages/home/data/profile_local_data.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/home/widgets/add_change_dialog.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/widgets/add_or_change_club_dialog.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/widgets/bottom_nav_bar_widget.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/home/widgets/changing_page_dialog.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/home/widgets/welcome_reset_password_dialog.dart` | ⚠️ Needs Minor Refactor | Widget |

### Edit Profile

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/edit_profile/edit_profile_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/edit_profile/edit_profile_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/edit_profile/edit_profile_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/edit_profile/edit_profile_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/edit_profile/edit_profile.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/edit_profile/widgets/caddie_info_view.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/edit_profile/widgets/notification_preferences_view.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/edit_profile/widgets/other_caddie_info_fields_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/edit_profile/widgets/widgets.dart` | ⏭️ Skip for Now | None |

### Contacts

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/contacts/contacts_controller.dart` | ✅ Unit Testable | Unit |
| `lib/app/pages/contacts/contact_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/contacts/contacts_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/contacts/contacts_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/contacts/contacts.dart` | ⏭️ Skip for Now | None |

### Rules

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/rules/rules_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/rules/rules_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/rules/rules_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/rules/rules_view.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/rules/rules.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/rules/widgets/caddie_agreement_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/rules/widgets/club_rules_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/rules/widgets/widgets.dart` | ⏭️ Skip for Now | None |

### History

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/history/history_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/history/history_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/history/history_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/history/history_view.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/history/patch_amount_dialog.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/history/history.dart` | ⏭️ Skip for Now | None |

### Area Assignments

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/area_assignments/area_assignments_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/pages/area_assignments/area_assignments_presenter.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/area_assignments/area_assignments_binding.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/area_assignments/area_assignments_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/area_assignments/area_assignments.dart` | ⏭️ Skip for Now | None |
| `lib/app/pages/area_assignments/widgets/assigned_area_assignments_view.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/pages/area_assignments/widgets/assigned_pdf_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/area_assignments/widgets/available_area_assignments_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/area_assignments/widgets/call__dialog_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/area_assignments/widgets/info_dialog.dart` | ✅ Unit Testable | Widget |
| `lib/app/pages/area_assignments/widgets/widgets.dart` | ⏭️ Skip for Now | None |

### Region Preferences

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/pages/region_preferences/region_notifications_preferences.dart` | ❌ Needs Major Refactor | Widget + Integration |
| `lib/app/pages/region_preferences/how_to_use_region_assignments_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/pages/region_preferences/region_preferences.dart` | ⏭️ Skip for Now | None |

### Shared App Widgets

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/widgets/app_logo_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/appbar_drop_down_widget.dart` | ❌ Needs Major Refactor | Widget |
| `lib/app/widgets/background_cover_image_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/center_circular_progress_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/custom_app_bar_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/custom_shimmer.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/widgets/custom_web_view.dart` | ❌ Needs Major Refactor | Integration |
| `lib/app/widgets/form_field_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/form_submit_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/media_bottom_sheet.dart` | ❌ Needs Major Refactor | Widget/Integration |
| `lib/app/widgets/no_internet_widget.dart` | ✅ Unit Testable | Widget |
| `lib/app/widgets/widgets.dart` | ⏭️ Skip for Now | None |

### App Utilities, Navigation, Theme, Root

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/app/utils/utility.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/utils/asset_constants.dart` | ⏭️ Skip for Now | None |
| `lib/app/utils/utils.dart` | ⏭️ Skip for Now | None |
| `lib/app/utils/exception/data_exception.dart` | ✅ Unit Testable | Unit |
| `lib/app/utils/exception/network_exception.dart` | ✅ Unit Testable | Unit |
| `lib/app/utils/exception/exception.dart` | ⏭️ Skip for Now | None |
| `lib/app/utils/managers/media_manager.dart` | ❌ Needs Major Refactor | Integration |
| `lib/app/utils/managers/media_upload_service.dart` | ❌ Needs Major Refactor | Integration |
| `lib/app/utils/strings/string_constants.dart` | ⏭️ Skip for Now | None |
| `lib/app/utils/strings/translation_file.dart` | ⏭️ Skip for Now | None |
| `lib/app/navigators/app_pages.dart` | ⏭️ Skip for Now | None |
| `lib/app/navigators/app_routes.dart` | ⏭️ Skip for Now | None |
| `lib/app/navigators/route_management.dart` | ⏭️ Skip for Now | Integration |
| `lib/app/navigators/navigators.dart` | ⏭️ Skip for Now | None |
| `lib/app/navigators/dynamic_routing/navigation_service.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/navigators/dynamic_routing/layout_template.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/app/navigators/dynamic_routing/router.dart` | ⚠️ Needs Minor Refactor | Integration |
| `lib/app/navigators/dynamic_routing/locator.dart` | ⏭️ Skip for Now | None |
| `lib/app/navigators/dynamic_routing/route_param_notifier.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/app/navigators/dynamic_routing/routing_data.dart` | ✅ Unit Testable | Unit |
| `lib/app/navigators/dynamic_routing/dynamic_routing_export.dart` | ⏭️ Skip for Now | None |
| `lib/app/theme/colors_value.dart` | ⏭️ Skip for Now | None |
| `lib/app/theme/dimens.dart` | ⏭️ Skip for Now | None |
| `lib/app/theme/styles.dart` | ⏭️ Skip for Now | None |
| `lib/app/theme/theme.dart` | ⏭️ Skip for Now | None |
| `lib/app/app_constants.dart` | ⏭️ Skip for Now | None |
| `lib/app/app.dart` | ⏭️ Skip for Now | None |
| `lib/app/responsive.dart` | ✅ Unit Testable | Unit/Widget |
| `lib/app/pages/pages.dart` | ⏭️ Skip for Now | None |
| `lib/app_config.dart` | ✅ Unit Testable | Unit |
| `lib/app.dart` | ❌ Needs Major Refactor | Integration |
| `lib/main.dart` | ⏭️ Skip for Now | None |
| `lib/main_dev.dart` | ⏭️ Skip for Now | None |

### Domain, Data, Device

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/domain/domain.dart` | ⏭️ Skip for Now | None |
| `lib/domain/repositories/domain_repository.dart` | ✅ Unit Testable | Unit/Mock Contract |
| `lib/domain/repositories/repository.dart` | ❌ Needs Major Refactor | Unit |
| `lib/domain/repositories/repositories.dart` | ⏭️ Skip for Now | None |
| `lib/domain/usecases/auth_usecases.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/usecases/feed_usecases.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/usecases/local_usecases.dart` | ✅ Unit Testable | Unit |
| `lib/domain/usecases/profile_usecases.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/usecases/schedule_usecases.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/usecases/usercases.dart` | ⏭️ Skip for Now | None |
| `lib/domain/services/common_service.dart` | ✅ Unit Testable | Unit |
| `lib/domain/services/notification_service.dart` | ❌ Needs Major Refactor | Integration |
| `lib/domain/services/services.dart` | ⏭️ Skip for Now | None |
| `lib/domain/entities/enums.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/entities/entities.dart` | ⏭️ Skip for Now | None |
| `lib/domain/entities/models/models.dart` | ⏭️ Skip for Now | None |
| `lib/domain/entities/models/response_model.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/login/login_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/login/login_email_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/login/login_email_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/login/login_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/reset_password/reset_password_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/reset_password/reset_password_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/get_club_details/get_club_details_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/profile/profile_details_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/profile/patch_api_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/profile/patch_api_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/schedule/get_availability_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/schedule/post_availability_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/schedule/post_availability_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/schedule/patch_assignment_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/schedule/patch_assignment_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/history/history_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/contact/contact_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/contact/contact_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/rules/rules_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/rules/post_acknowledgement_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/rules/post_acknowledge_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/notifications/notifications_preferences_body.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/notifications/notification_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/feed_page/feed_page_response.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/domain/entities/models/area_assignments/area_assignments_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/area_assignments/area_assignments.dart` | ⏭️ Skip for Now | None |
| `lib/domain/entities/models/region_clubs/get_region_clubs_response.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/region_clubs/region_clubs.dart` | ⏭️ Skip for Now | None |
| `lib/domain/entities/models/token_list/token_list_model.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/upload_model/multi_part_request_model.dart` | ✅ Unit Testable | Unit |
| `lib/domain/entities/models/media_management/image_picker_result.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/data/data.dart` | ⏭️ Skip for Now | None |
| `lib/data/data_constants.dart` | ⏭️ Skip for Now | None |
| `lib/data/helpers/api_wrapper.dart` | ❌ Needs Major Refactor | Unit |
| `lib/data/helpers/connect_helper.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/data/helpers/helpers.dart` | ⏭️ Skip for Now | None |
| `lib/data/repositories/data_repositories.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/device/device.dart` | ⏭️ Skip for Now | None |
| `lib/device/device_constants.dart` | ⏭️ Skip for Now | None |
| `lib/device/repositories/device_repositories.dart` | ⚠️ Needs Minor Refactor | Unit |

## 3. Tightly Coupled Code — Identified Blockers

| File | What test CANNOT be written today? | What dependency/coupling is blocking it? | Smallest refactor to unblock testing | Risk this refactor introduces | Suggested Priority |
|---|---|---|---|---|---|
| `lib/app/pages/login/login_controller.dart` | A fully isolated `submitLogin` test that verifies success navigation and failure dialog without pumping Get dialogs. | Calls `RouteManagement.goToHome()`, `Utility.closeDialog()`, `Utility.showADialogWithAction()`, and reads browser `href`. | Inject a small `LoginUiActions`/`NavigationPort` into the controller; pass a fake in tests. | Route behavior can drift if the port maps the wrong route. | P0 Auth |
| `lib/app/pages/login/club_login_view.dart` | A clean widget test that pumps `ClubLoginView` without pre-registering global Get state. | Uses module-level `Get.find<LoginController>()`, so the dependency is resolved before the widget is pumped. | Move `Get.find<LoginController>()` inside `build`, or pass controller as optional constructor parameter. | Low; widget lifecycle changes slightly. | P0 Auth |
| `lib/domain/repositories/repository.dart` | Unit test for login request body creation and saved local values without Firebase/platform plugins. | Calls `Utility.showLoadingDialog`, `Utility.getDeviceId`, `Utility.getDeviceModel`, `GetPlatform`, and `FirebaseMessaging.instance`. | Extract `DeviceInfoPort`, `LoadingPort`, and `PushMessagingPort`; keep existing defaults in production. | Medium; login side effects are business-critical. | P0 Auth |
| `lib/data/helpers/api_wrapper.dart` | Unit test for 401 handling without registering `HomeController` and invoking logout UI. | `_returnResponse` calls `Get.find<HomeController>()` and can delete/switch user state from the HTTP layer. | Inject an `UnauthorizedHandler` or return an auth-error response and let repository/controller decide. | Medium; session-expiry behavior can regress. | P1 Shared |
| `lib/app/pages/schedule_ui/schedule_ui_controller.dart` | Pure unit tests for availability body generation and date/slot transforms without ticker/tab setup. | Controller mixes `TabController`, `GetTickerProviderStateMixin`, `DateTime.now`, `Utility`, presenter API calls, and GetX state. | Extract date/slot mapping into `ScheduleAvailabilityMapper` and inject clock. | Medium; scheduling date logic is sensitive. | P1 Schedule |
| `lib/app/pages/home/home_controller.dart` | Unit test for `changeTabIndex` unsaved-changes behavior without real feed/schedule/area controllers. | Has field-level `Get.find<FeedController>()`, `Get.find<RulesController>()`, `Get.find<AreaAssignmentsController>()`, and `Get.find<ScheduleUiController>()`; also calls `Get.dialog`. | Constructor-inject sibling controllers and a `DialogPort`. | Medium; tab navigation can regress. | P1 Home |
| `lib/app/pages/home/pages/feed/feed_view.dart` | Widget test for feed screen without Firebase listeners. | `initState` wires Firebase Messaging and local notifications directly. | Extract push-notification handling into injectable service. | Low to medium; notification display behavior can regress. | P2 Feed |
| `lib/app/pages/home/data/profile_local_data.dart` | Unit test that pressing profile items triggers expected navigation/dialogs. | Static builder directly calls `locator<NavigationService>()`, `RouteManagement`, `Get.dialog`, `GetPlatform`, and controller methods. | Accept `ProfileActions` callbacks or a `NavigationPort` as an optional parameter. | Low; profile menu taps can route incorrectly. | P2 Profile |
| `lib/app/pages/edit_profile/edit_profile_controller.dart` | Unit test for `profileDetails` populating fields without API/dialog side effects. | `onInit` immediately calls profile/rules APIs; failure path calls `Utility.closeDialog` and `Utility.showADialogWithAction`. | Guard initial fetch behind a constructor flag for tests, or extract field-population method. | Low; initialization timing changes. | P2 Edit Profile |
| `lib/app/pages/area_assignments/area_assignments_controller.dart` | Unit test for load state transitions and request patch flow without platform/share/url side effects. | `onInit` immediately calls two APIs; methods call `Utility.closeDialog`, `Share.share`, and `launchUrl`. | Add `autoLoad` constructor flag and inject `SharePort`/`UrlLauncherPort`. | Low. | P3 Area Assignments |
| `lib/domain/services/notification_service.dart` | Unit test for notification initialization. | Direct `Firebase.initializeApp`, `FirebaseMessaging.instance`, `FlutterLocalNotificationsPlugin`, and platform branches. | Inject Firebase/local-notification wrappers; integration-test the real implementation. | Medium; notification permission behavior can regress. | P3 Services |
| `lib/app.dart` | Widget test for `MyApp` or bootstrap without Firebase/Hive/global Get setup. | Calls `Firebase.initializeApp`, `Get.putAsync`, `DeviceRepository.init`, `SystemChrome`, and `runApp` in one function. | Extract `AppInitializer` and make `MyApp` accept locale/nav dependencies. | Medium; app startup order can regress. | P3 Bootstrap |

## 4. Refactoring Plan (Testing-Only, No Cleanup)

Only test-blocking refactors are listed here. No stylistic rewrites are required before tests.

### `lib/app/pages/login/login_controller.dart`

Current problem:

```dart
loginResponse = await _loginPresenter.loginApi(
    password: password, userName: userName);
saveResetPasswordStatus(true);
RouteManagement.goToHome();
update();
```

Exact change needed:

```dart
abstract class LoginNavigation {
  void goToHome();
  Future<void> goToLogin();
}

class LoginController extends GetxController {
  LoginController(this._loginPresenter, {LoginNavigation? navigation})
      : _navigation = navigation ?? GetLoginNavigation();

  final LoginNavigation _navigation;
}
```

Then call `_navigation.goToHome()` in `submitLogin`. Tests can assert a fake navigation call.

Estimated effort: **Small (< 1hr)**.

### `lib/app/pages/login/club_login_view.dart`

Current problem: module-level `Get.find<LoginController>()` resolves before the test can pump the widget.

Exact change needed:

```dart
class ClubLoginView extends StatelessWidget {
  ClubLoginView({super.key, LoginController? controller})
      : controller = controller ?? Get.find<LoginController>();

  final LoginController controller;
}
```

Estimated effort: **Small (< 1hr)**.

### `lib/domain/repositories/repository.dart`

Current problem:

```dart
Utility.showLoadingDialog();
var loginBody = LoginBody(
  deviceId: await Utility.getDeviceId(),
  deviceMake: await Utility.getDeviceOsVersion(),
  deviceModel: await Utility.getDeviceModel(),
  deviceTypeCode: GetPlatform.isWeb ? 'WEB' : GetPlatform.isAndroid ? 'ANDROID' : 'IOS',
  deviceOs: await Utility.getDeviceOsVersion(),
  browserVersion: await Utility.getAppVersion(),
  clubId: clubId,
);
...
await FirebaseMessaging.instance.subscribeToTopic(
    _deviceRepository.getStringValue(DeviceConstants.fcmTopic));
```

Exact change needed:

```dart
abstract class DeviceInfoPort {
  Future<String> deviceId();
  Future<String> osVersion();
  Future<String> model();
  Future<String> appVersion();
  String deviceTypeCode();
}

abstract class PushMessagingPort {
  Future<void> subscribeToTopic(String topic);
  Future<void> unsubscribeFromTopic(String topic);
  Future<void> deleteToken();
}
```

Inject these ports into `Repository`, preserving current implementations as defaults.

Estimated effort: **Large (1+ day)** because this is shared auth/session code.

### `lib/data/helpers/api_wrapper.dart`

Current problem:

```dart
case 401:
  final controller = Get.find<HomeController>();
  if (controller.clubList.length == 1) {
    controller.deleteSingleUser();
  } else if (controller.clubList.length >= 2) {
    ...
    controller.getDataAndShowDialogforDeleteandChange(...);
  }
```

Exact change needed:

```dart
abstract class UnauthorizedHandler {
  Future<void> handleUnauthorized();
}

class ApiWrapper {
  ApiWrapper({http.Client? client, UnauthorizedHandler? unauthorizedHandler});
}
```

Tests can then verify status code mapping without a real `HomeController`.

Estimated effort: **Medium (2-4hr)**.

### `lib/app/pages/schedule_ui/schedule_ui_controller.dart`

Current problem: testable logic is inside a UI controller with ticker, tabs, GetX, date clock, and API calls.

Extract this kind of logic:

```dart
bool checkDateForShowingCheckIn(String date) {
  if (todaysDate == DateTime.parse(DateFormat('yyyy-MM-dd').format(DateTime.parse(date)))) {
    return true;
  } else {
    return false;
  }
}
```

Exact change needed:

```dart
class ScheduleDateRules {
  ScheduleDateRules(this.today);
  final DateTime today;

  bool isSameScheduleDate(String date) {
    final normalizedToday = DateTime(today.year, today.month, today.day);
    final parsed = DateTime.parse(date);
    return DateTime(parsed.year, parsed.month, parsed.day) == normalizedToday;
  }
}
```

Estimated effort: **Medium (2-4hr)** for date rules; **Large (1+ day)** for availability POST body extraction.

### `lib/app/pages/home/home_controller.dart`

Current problem:

```dart
final feedController = Get.find<FeedController>();
final rulesController = Get.find<RulesController>();
final areaAssignmentController = Get.find<AreaAssignmentsController>();
ScheduleUiController scheduleUiController = Get.find();
```

Exact change needed: constructor-inject those collaborators with default `Get.find` fallback only in binding.

Estimated effort: **Medium (2-4hr)**.

### `lib/app/pages/home/data/profile_local_data.dart`

Current problem:

```dart
onTap: () {
  locator<NavigationService>().navigateTo(Routes.editProfile);
},
...
Get.dialog<void>(AddOrChangeClubDialog(...));
```

Exact change needed: pass `ProfileMenuActions` into `buildSections`, keeping a default production adapter.

Estimated effort: **Small (< 1hr)**.

## 5. Refactoring Priority Order

1. `lib/app/pages/login/login_controller.dart` — Auth is the team’s first module, and the validation tests already exist. Injecting navigation/dialog actions unlocks the most auth behavior with low risk.
2. `lib/app/pages/login/club_login_view.dart` — Also Auth; a very small change removes a major widget-test blocker caused by top-level `Get.find`.
3. `lib/domain/repositories/repository.dart` — Auth login depends on this class. Refactor after controller/view because it has higher risk, but it is necessary for reliable login unit tests.
4. `lib/data/helpers/api_wrapper.dart` — Shared by all API modules; the 401 handler couples network tests to `HomeController`.
5. `lib/data/helpers/connect_helper.dart` — Inject `ApiWrapper` after `ApiWrapper` becomes mockable; this unlocks HTTP request tests.
6. `lib/app/pages/schedule_ui/schedule_ui_controller.dart` — High business value and many date/availability edge cases; extract pure schedule logic before testing the whole controller.
7. `lib/app/pages/home/home_controller.dart` — Very shared, but risky. Do after Auth and HTTP seams so fakes are available.
8. `lib/app/pages/home/data/profile_local_data.dart` — Moderate value, small risk; test profile menu routing and sign-out options.
9. `lib/app/pages/edit_profile/edit_profile_controller.dart` — Good candidate after shared fake presenter patterns exist.
10. `lib/app/pages/area_assignments/area_assignments_controller.dart` — No current coverage; small controller methods are easy once auto-load is disabled in tests.
11. `lib/app/pages/region_preferences/region_notifications_preferences.dart` — No coverage and coupled to `HomeController`; best tackled after `HomeController` injection.
12. `lib/domain/services/notification_service.dart` — Platform-heavy; keep for integration/fake platform tests after core modules are stable.
13. `lib/app.dart` — Lowest priority for unit tests; cover via integration smoke after app initialization is made configurable.

## 6. Unit Testing Plan — Module by Module

### Auth / Login

Test order:

1. `login_controller.dart`
2. `auth_usecases.dart`
3. `repository.dart` login paths after refactor
4. Login DTOs: `login_body.dart`, `login_response.dart`, `login_email_body.dart`, `login_email_response.dart`

Methods and cases:

- `checkIfIdIsValid(value)`: pass `''`, `'caddie1'`; verify `isIdValid` and `userName`.
- `checkIfPasswordIsValid(value)`: pass `''`, `'secret'`; verify `isPasswordValid` and `password`.
- `enableButton()`: cover both true and false combinations of `isIdValid`/`isPasswordValid`.
- `checkIfClubValid(value)` and `enableClubLogin()`: pass empty and non-empty club IDs.
- `emailValidator(value)`: pass empty, invalid, valid email; verify returned message and `isEmailEnabled`.
- `loginWithEmail()`: mock `LoginPresenter.loginApiEmail`; verify success/failure UI action after dialog port injection.
- `submitLogin()`: mock `LoginPresenter.loginApi`; verify presenter parameters, reset-password status, and navigation.
- `Repository.loginApi`: fake device/data repository, fake device info, fake push messaging; verify `LoginBody` values, saved token/user fields, FCM topic subscription branch.

Mocks/stubs needed:

- Fake `LoginPresenter` or fake `AuthUseCases`.
- Fake `LoginNavigation`/dialog actions.
- Fake `DeviceRepository`, `DataRepository`, `DeviceInfoPort`, `PushMessagingPort`.

### Splash

Test files:

- `splash_controller.dart`

Methods:

- Initial route decision based on `AuthUseCases.isUserLoggedIn`.
- Failure/edge behavior when local storage returns default values.

Mocks/stubs:

- Fake `SplashPresenter` or fake `AuthUseCases`.
- Fake navigation port for `RouteManagement.goToHome` / login route.

### Schedule UI

Test files:

- `schedule_ui_controller.dart` after extracting pure mappers.
- `get_availability_response.dart`, `post_availability_body.dart`, `patch_assignment_body.dart`.

Methods:

- `checkDateForShowingAvailableLoopDateOrnot(date)`: pass yesterday/today/tomorrow.
- `checkDateForShowingConfirmAndDecline(date)`: pass before/today/after.
- `checkDateForShowingCheckIn(dateString)`: pass today and another day.
- `isScheduleMarkedNotAssigned(index)`: pass availability rows with `markedAsNotAssinged` true/false and assignment list empty/non-empty.
- `checkTwoSlotValueisValid(availableEndTime, teaEndtime)`: pass end times with less/equal/more than 2-hour gap.
- `changeTimeTo12Hours(sTime, eTime)`: pass `08:00`/`17:30`; verify formatted string.
- `initialSfRangeValue(startTime, endTime, date)`: verify start/end DateTime values.
- Extracted POST body builder: pass selected one-slot, two-slot, unavailable date, no-change cases; verify `PostAvailabilityBody`.

Mocks/stubs:

- Fake `SchedulePresenter`.
- Fake clock/today value.
- For widget tests, pre-register a controller with prepared `slotDetailsCustomList`.

### Home / Feed / Profile

Test files:

- `home_controller.dart`
- `feed_controller.dart`
- `profile_local_data.dart`
- profile model files

Methods:

- `HomeController.visible`, `changeAddChangeClubVisibility`, `checkIsvisible`, `checkVisibiltyAppBar`.
- `HomeController.changeTabIndex(index)`: pass from schedule tab to feed/profile with `isThereNewChange` true/false; verify dialog action and API refresh calls.
- `HomeController.selectAllEnableClubs`, `selectAllNotifications`, `checkingValueForEnableShowAll`: pass fake `GetRegionClubs` data.
- `FeedController.updatePageStatus`, `updateFilterOptions`, `checkNewMessage`, `showMessage`, `noMessageIsThere`.
- `FeedController.isSelectedAll`, `isLoopAssignment`, `isAnnouncement`: verify `type` and fake `getNewsFeed` call.
- `FeedController.getFeedType(feed)`: pass announcement type and normal subject.
- `ProfileLocalData.buildSections`: pass `regionAssignment` true/false, `clubList` length 1/2; verify sections/items and tap callbacks through fake actions.

Mocks/stubs:

- Fake `HomePresenter`.
- Fake sibling controllers for home.
- Fake navigation/dialog/profile menu actions.
- Fake feed data and rules response.

### Edit Profile

Test files:

- `edit_profile_controller.dart`
- edit profile widgets already in `test/app/edit_profile`

Methods:

- `isReadStatus`, `infoFieldsVisibility`, `checkVisibilty`.
- `checkingValueForEnableAllPush`: pass all true, one false, defaults.
- `checkingValueForEnableAllEmail`: pass all true, one false. Note current code repeats `assignedLoopAssignmentEmail`.
- Extracted field population from `profileDetails`: pass fake profile response; verify text controllers and notification booleans.
- `patchNotificationPreferences`: verify `NotificationsPreferencesBody` generated from controller state.

Mocks/stubs:

- Fake `EditProfilePresenter`.
- Fake dialog/snackbar actions.
- Fake date picker only if testing `slectDateOfBirth`.

### Contacts

Test files:

- `contacts_controller.dart`

Methods:

- `onChangeType(value)`: pass each dropdown option; verify selected state.
- `dropDown(val)`: pass `Concern about a member`, `General Question`, `Other`, `Cancel My Loop`; verify `msgType`.
- `onChange(wayToReply)`: pass phone/email/null; verify selected.
- `checkIfMsgIsValid(value)` and `enableMsg()`: pass empty/non-empty.
- `postContact()`: pass phone/email and empty/custom `msgType`; verify presenter receives reply `0` or `1`.

Mocks/stubs:

- Fake `ContactPresenter`.
- Fake snackbar/dialog actions for success/failure.

### Rules

Test files:

- `rules_controller.dart`

Methods:

- `isEnabled`: verify toggle.
- `clubSetting`: pass fake rules response; verify `pdfUrl`, `rulesText`, `phone`, `agreementAgreed`, `rulePdf`.
- `shareAgreement`, `shareRules`: verify fake share service receives URL.
- `postAcknowledgement`: verify presenter called and navigation triggered.

Mocks/stubs:

- Fake `RulesPresenter`.
- Fake share/url launcher/navigation ports.

### History

Test files:

- `history_controller.dart`

Methods:

- `startFunction`: inject clock; verify last-7-days start and `getHistory` call.
- `tabIndexChange(index)`: pass 0/1/2/3; verify custom visibility and date range call.
- `searchHistory(searchText)`: pass empty and non-empty; verify presenter call.
- `patchAmount(amount, id, isLoading)`: verify delegation.

Mocks/stubs:

- Fake `HistoryPresenter`.
- Fake clock.
- Pre-seeded `HistoryResponse(data: [])` to avoid null assertions.

### Area Assignments

Test files:

- `area_assignments_controller.dart`

Methods:

- `loopRequestSwitch`, `loopRequestSwitch1`, `loopRequestSwitch2`, `confirmToggle`.
- `getAvailableAreaAssignments`: verify status `'1'` and loading flags.
- `getAssignedAreaAssignments`: verify status `'2'` and loading flags.
- `patchAreaAssignmentsRequest(id, status)`: verify patch then available refresh.
- `emailRedirecting(email)`: after URL port extraction, verify `mailto` URI.

Mocks/stubs:

- Fake `AreaAssignmentsPresenter`.
- Fake URL/share ports.

### Region Preferences

Test files:

- `region_notifications_preferences.dart` after home/controller injection.
- `home_controller.dart` region methods.

Methods:

- Region assignment toggle list creation.
- Select all notification/assignment.
- Patch payload generation.
- Web/mobile route behavior through fake navigation.

Mocks/stubs:

- Fake `HomeController`/`HomePresenter`.
- Fake navigation.

### Domain/Data/Device

Test order:

1. JSON DTOs with fixtures.
2. Use cases with fake repository.
3. `DataRepository` with fake `ConnectHelper`.
4. `ConnectHelper` with fake `ApiWrapper`.
5. `ApiWrapper` with fake `http.Client` after refactor.
6. `DeviceRepository` CRUD with `init(isTest: true)`.

Edge cases:

- Empty response body should throw `DataException`.
- `hasError = true` should throw `NetworkException` or return error response depending on method.
- Missing nullable JSON fields should not crash where model fields are nullable.
- Hive missing keys should return project defaults.

## 7. Integration & Widget Testing Opportunities

Widget tests can begin in parallel now:

- `confirm_show_dialog.dart`, `decline_show_dialog.dart`, `changing_page_dialog.dart`, `add_change_dialog.dart`, `info_dialog.dart`.
- `available_btn_widget.dart`, `button_widget.dart`, `form_field_widget.dart`, `form_submit_widget.dart`, `app_logo_widget.dart`, `custom_app_bar_widget.dart`.
- `caddie_info_view.dart`, `notification_preferences_view.dart`, `other_caddie_info_fields_widget.dart`.
- `message_board.dart`, `profile_section.dart`, `profile_tiles_widget.dart`.

Widget tests that need minor refactor first:

- `club_login_view.dart`
- `login_view.dart`
- `schedule_assignment_card.dart`
- `schedule_day_date.dart`
- `profile_header.dart`
- `contacts_view.dart`
- `patch_amount_dialog.dart`
- `available_area_assignments_view.dart`
- `how_to_use_region_assignments_view.dart`

Integration/E2E flows:

- Production/dev bootstrap smoke: `main.dart`/`main_dev.dart` -> splash.
- Auth: club login -> username/password login -> home.
- Reset password first-login flow.
- Home bottom tab flow: feed -> schedule -> area assignments/profile.
- Schedule: load availability -> edit slot -> save -> refresh.
- Assignment flow: confirm, decline, check in.
- Feed: filter all/announcement/loop assignment and open PDF/web content.
- Profile: edit profile, update notification preferences.
- Rules: open agreement/rules PDF, acknowledge agreement.
- Contacts: submit contact form.
- History: filter by 7/30/365/custom, patch amount.
- Area Assignments: request/unrequest, view/share assigned PDF, call/email contact.
- Region Preferences: fetch region clubs, toggle assignment/notifications, save.

Can run in parallel with unit testing:

- Pure widget tests for dialog/form widgets.
- JSON model tests.
- Use case delegation tests.
- DeviceRepository local storage tests.

Should wait for refactor:

- Full Auth controller success/failure flow.
- `ApiWrapper` 401 handling.
- Schedule slot mapper and POST body tests.
- Home tab unsaved-changes tests.
- Region preferences view tests.

## 8. Definition of Done — Per File

Apply this DoD to every tested file in the inventory.

| File Classification | Minimum Coverage Target | Happy Paths | Error/Edge Cases | CI Passing | Peer Reviewed |
|---|---:|---|---|---|---|
| ✅ Unit Testable DTO/model/use case | 90%+ line coverage for that file | Yes | Yes, for null/empty/invalid JSON or repository errors | Yes | Yes |
| ✅ Unit Testable widget | Coverage is secondary; assert rendered text/keys/callbacks at key breakpoints | Yes | Yes, missing/empty props where applicable | Yes | Yes |
| ⚠️ Needs Minor Refactor | 80%+ after smallest refactor, or explicit reason if UI/platform code remains unmeasured | Yes | Yes, especially failure branches unlocked by fake dependency | Yes | Yes |
| ❌ Needs Major Refactor | Do not chase coverage before refactor; DoD is approved seam + core behavior tests | Yes for extracted behavior | Yes for extracted behavior | Yes | Yes |
| ⏭️ Skip for Now | Not coverage-driven | N/A | N/A | N/A | N/A |

Per-file completion record should state:

- File path.
- Classification before work.
- Refactor done: Yes/No.
- Test type added: Unit/Widget/Integration.
- Happy path covered: Yes/No.
- Error/edge cases covered: Yes/No.
- Coverage for file or reason coverage is not the metric.
- CI command run and result.
- Reviewer name.

## 9. Progress Tracking Metrics

For each completed file, log this in the shared Google Doc:

| Field | What to Record |
|---|---|
| Module | Auth, Schedule, Home, etc. |
| File path | Example: `lib/app/pages/login/login_controller.dart` |
| Original classification | ✅ / ⚠️ / ❌ / ⏭️ |
| Final classification | What it is after refactor/test |
| Refactor PR/link | Link or branch |
| Test PR/link | Link or branch |
| Test type | Unit / Widget / Integration |
| Test file path | Example: `test/app/login/login_controller_test.dart` |
| Coverage | File coverage or “not metric: widget assertions” |
| Happy path | Yes/No |
| Edge/error cases | Yes/No |
| CI result | Command and pass/fail |
| Reviewer | Name |
| Notes | Remaining blockers |

Report refactoring and testing separately:

- Refactor done means the smallest approved test seam is merged.
- Testing done means tests are committed, pass locally, and pass in CI.
- A file can be “refactor done” but not “testing done”.

Weekly numbers to report:

- Files completed by module.
- Files refactored but not yet tested.
- Tests added by type: unit/widget/integration.
- Current `flutter test` status.
- Current `flutter test --coverage` percentage.
- Number of remaining ⚠️ files.
- Number of remaining ❌ files.
- Blocked files with owner and blocker.

## 10. Consistency Standards Across Developers

Recommended test structure:

```text
test/
  helpers/
    pump_app.dart
    test_bindings.dart
    test_storage.dart
  fakes/
    fake_repository.dart
    fake_presenters.dart
    fake_navigation.dart
  fixtures/
    login_response.json
    profile_details_response.json
  app/
    login/
    schedule_ui/
    home/
    edit_profile/
  domain/
    entities/
    usecases/
  data/
  device/
integration_test/
  app_smoke_test.dart
```

Naming conventions:

- Test files must end with `_test.dart`.
- Mirror the source file name: `login_controller.dart` -> `login_controller_test.dart`.
- Use `group('LoginController', ...)`.
- Use Given/When/Then-style descriptions:
  - `test('given empty username when validating id then disables login', () {})`
  - `testWidgets('given confirm dialog when yes tapped then invokes callback', (tester) async {})`

Every test file header should include:

```dart
// Tests for: lib/app/pages/login/login_controller.dart
// Scope: unit tests for validation and login state transitions.
// Mocks: FakeLoginPresenter, FakeLoginNavigation.
```

Shared mocks/utilities:

- Prefer `mocktail` for hand-written mocks without code generation.
- Put fakes in `test/fakes/`.
- Put JSON fixtures in `test/fixtures/`.
- Put GetX pump helpers in `test/helpers/pump_app.dart`.
- Always call `Get.testMode = true` and `Get.reset()` in `setUp`/`tearDown` for tests that touch GetX.
- Do not hit live APIs in unit tests. Live API tests should move to `integration_test/` or be tagged and gated by environment.

## 11. Approval & Review Process (Refactoring)

Before starting any refactor for testability, paste this template into the shared Google Doc.

```markdown
## Testing Refactor Approval Request

**Developer:**
**Date:**
**Module:**
**File(s):**

### 1. What test cannot be written today without this refactor?

Example:
I cannot unit-test `LoginController.submitLogin()` success/failure because it directly calls static navigation and dialog methods.

### 2. What specific coupling or dependency is blocking it?

Example:
`RouteManagement.goToHome()`, `Utility.showADialogWithAction()`, and `Utility.closeDialog()` are called directly from the controller.

### 3. What is the smallest change that unblocks the test?

Example:
Inject a `LoginNavigation`/`LoginUiActions` interface with default GetX implementation and use a fake in tests.

### 4. What regression risk does this refactor carry?

Example:
Login could route to the wrong page if the default adapter maps incorrectly.

### 5. How will the regression risk be covered?

Example:
Add unit tests for controller success/failure and a widget/integration smoke test for login navigation.

### 6. Who reviewed and approved it?

Reviewer:
Approval date:
```

## 12. Real Examples from THIS Codebase

### Example 1: Auth Controller Navigation Coupling

Current code in `lib/app/pages/login/login_controller.dart`:

```dart
void submitLogin() async {
  try {
    loginResponse = await _loginPresenter.loginApi(
        password: password, userName: userName);
    saveResetPasswordStatus(true);
    RouteManagement.goToHome();
    update();
  } catch (exception) {
    Utility.closeDialog();
    Utility.showADialogWithAction(StringConstants.enterValidUserName);
    Utility.printELog(exception.toString());
  }
}
```

Refactored shape:

```dart
abstract class LoginUiActions {
  void goToHome();
  void showInvalidLogin();
  void closeDialog();
}

class LoginController extends GetxController {
  LoginController(this._loginPresenter, {LoginUiActions? uiActions})
      : _uiActions = uiActions ?? GetLoginUiActions();

  final LoginPresenter _loginPresenter;
  final LoginUiActions _uiActions;

  Future<void> submitLogin() async {
    try {
      loginResponse = await _loginPresenter.loginApi(
        password: password,
        userName: userName,
      );
      saveResetPasswordStatus(true);
      _uiActions.goToHome();
      update();
    } catch (_) {
      _uiActions.closeDialog();
      _uiActions.showInvalidLogin();
    }
  }
}
```

Unit test unlocked:

```dart
test('given valid credentials when submit succeeds then saves reset status and navigates home', () async {
  final presenter = FakeLoginPresenter()..loginResponse = validLoginResponse;
  final ui = FakeLoginUiActions();
  final controller = LoginController(presenter, uiActions: ui)
    ..userName = 'caddie'
    ..password = 'secret';

  await controller.submitLogin();

  expect(presenter.lastUserName, 'caddie');
  expect(presenter.lastPassword, 'secret');
  expect(ui.didGoHome, isTrue);
});
```

### Example 2: HTTP Layer Coupled to Home Controller

Current code in `lib/data/helpers/api_wrapper.dart`:

```dart
case 401:
  final controller = Get.find<HomeController>();
  if (controller.clubList.length == 1) {
    controller.deleteSingleUser();
  } else if (controller.clubList.length >= 2) {
    var index = controller.clubList
        .indexWhere((element) => element.token == controller.tokens);
    BuildContext? context;
    controller.getDataAndShowDialogforDeleteandChange(
      controller.clubList[index].token.toString(),
      ClubEvent.delete,
      context,
      true,
      false,
      controller.clubList[index].fcmTopic.toString(),
    );
  }
```

Refactored shape:

```dart
abstract class UnauthorizedHandler {
  Future<void> call();
}

class ApiWrapper {
  ApiWrapper({
    required this.client,
    required this.unauthorizedHandler,
  });

  final http.Client client;
  final UnauthorizedHandler unauthorizedHandler;

  Future<ResponseModel> returnResponse(http.Response response) async {
    if (response.statusCode == 401) {
      await unauthorizedHandler();
      return ResponseModel(
        data: response.body,
        hasError: true,
        errorCode: response.statusCode,
      );
    }
    ...
  }
}
```

Unit test unlocked:

```dart
test('given 401 response when returning response then invokes unauthorized handler', () async {
  final handler = FakeUnauthorizedHandler();
  final wrapper = ApiWrapper(client: FakeHttpClient(), unauthorizedHandler: handler);

  final result = await wrapper.returnResponse(http.Response('unauthorized', 401));

  expect(result.hasError, isTrue);
  expect(result.errorCode, 401);
  expect(handler.called, isTrue);
});
```

### Example 3: Schedule Date Logic Buried in UI Controller

Current code in `lib/app/pages/schedule_ui/schedule_ui_controller.dart`:

```dart
bool checkDateForShowingCheckIn(String date) {
  if (todaysDate == DateTime.parse(DateFormat('yyyy-MM-dd').format(DateTime.parse(date)))) {
    return true;
  } else {
    return false;
  }
}
```

Refactored shape:

```dart
class ScheduleDateRules {
  ScheduleDateRules({required DateTime today})
      : today = DateTime(today.year, today.month, today.day);

  final DateTime today;

  bool shouldShowCheckIn(String date) {
    final parsed = DateTime.parse(date);
    final slotDate = DateTime(parsed.year, parsed.month, parsed.day);
    return slotDate == today;
  }
}
```

Unit test unlocked:

```dart
test('given slot date equals today when checking check-in visibility then returns true', () {
  final rules = ScheduleDateRules(today: DateTime(2026, 5, 19, 15, 30));

  expect(rules.shouldShowCheckIn('2026-05-19T08:00:00.000'), isTrue);
  expect(rules.shouldShowCheckIn('2026-05-20T08:00:00.000'), isFalse);
});
```

## 13. Recommended `flutter_test` Setup

Current `pubspec.yaml` has only:

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
```

Recommended:

```yaml
dependencies:
  http: ^1.2.2 # ApiWrapper imports package:http directly; make it explicit.

dev_dependencies:
  flutter_test:
    sdk: flutter
  integration_test:
    sdk: flutter
  mocktail: ^1.0.4
  fake_async: ^1.3.1
  firebase_core_platform_interface: ^5.3.0
  plugin_platform_interface: ^2.1.8
```

Use `mockito` + `build_runner` only if the team prefers generated mocks:

```yaml
dev_dependencies:
  mockito: ^5.4.4
  build_runner: ^2.4.13
```

Project-specific setup:

- Rename `test/variables.dart` into focused helpers over time:
  - `test/helpers/test_bindings.dart`
  - `test/helpers/pump_app.dart`
  - `test/helpers/test_storage.dart`
- Keep `DeviceRepository().init(isTest: true)` for local storage tests.
- Add `Get.reset()` in `tearDown`.
- Move live API tests or API smoke tests out of regular unit tests.
- Add fixture JSON files for domain model tests.

Commands:

```bash
flutter test
flutter test --coverage
flutter test test/app/login/login_controller_test.dart
flutter test test/app/schedule_ui/
flutter test --name "given valid credentials"
flutter test integration_test/
```

Current CI already runs:

- `flutter analyze`
- `flutter test` on `main`
- `flutter test --coverage` with 75% minimum on configured developer/staging branches

## Summary Table

| Module | Files Total | Testable Now | Needs Refactor | Skip | Estimated Effort |
|---|---:|---:|---:|---:|---|
| Auth/Login | 8 | 1 | 4 | 3 | Medium |
| Splash | 5 | 0 | 2 | 3 | Small |
| Schedule UI | 15 | 4 | 7 | 4 | Large |
| Home/Feed/Profile | 32 | 8 | 17 | 7 | Large |
| Edit Profile | 9 | 3 | 2 | 4 | Medium |
| Contacts | 5 | 1 | 1 | 3 | Small |
| Rules | 8 | 0 | 4 | 4 | Medium |
| History | 6 | 0 | 3 | 3 | Medium |
| Area Assignments | 11 | 1 | 6 | 4 | Medium |
| Region Preferences | 3 | 0 | 2 | 1 | Medium |
| Shared Widgets | 12 | 7 | 4 | 1 | Medium |
| App Utilities/Navigation/Theme/Root | 41 | 6 | 12 | 23 | Large |
| Domain/Data/Device | 58 | 33 | 15 | 10 | Large |

