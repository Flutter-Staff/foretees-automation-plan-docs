# ForeTees Staff (`flutter_foretees_staff`) — Testing Readiness Report
Generated from the current Flutter codebase on 2026-05-18. Scope includes `lib/` (713 Dart files) and the current `test/` folder (4 Dart files).

## Index

1. [Project Overview](#1-project-overview)
2. [Testing Scope Inventory](#2-testing-scope-inventory)
3. [Tightly Coupled Code — Identified Blockers](#3-tightly-coupled-code-identified-blockers)
4. [Refactoring Plan (Testing-Only, No Cleanup)](#4-refactoring-plan)
5. [Refactoring Priority Order](#5-refactoring-priority-order)
6. [Unit Testing Plan — Module by Module](#6-unit-testing-plan)
7. [Integration & Widget Testing Opportunities](#7-integration-widget-testing)
8. [Definition of Done — Per File](#8-definition-of-done)
9. [Progress Tracking Metrics](#9-progress-tracking-metrics)
10. [Consistency Standards Across Developers](#10-consistency-standards)
11. [Real Examples from THIS Codebase](#11-real-examples)
12. [Recommended `flutter_test` Setup](#12-flutter-test-setup)

---

<a id="1-project-overview"></a>

## 1. Project Overview
- **What it does:** ForeTees Staff is a multi-platform staff operations app for club workflows: login, dashboard access, food ordering, location/member check-in, valet, event check-in, inventory counts, lesson pro, member photos, photo gallery, push notifications, starter/timesheets, golf bag requests, MQTT updates, and FT Messaging launch.
- **Entry points:** `lib/main_dev.dart`, `lib/main_staging.dart`, and `lib/main_prod.dart` call `start(Environment...)` in `lib/main.dart`. `lib/main.dart` performs Firebase, Crashlytics, FCM, window, DI, shared preferences, timezone, router, and BLoC provider bootstrapping.
- **Architecture:** Clean Architecture style with `presentation` BLoCs/Cubits, `domain` use cases/repository contracts, `data` repository implementations/models/mappers/local managers, `remote` services/network client, `get_it` service locator via `InjectionUtils`, `flutter_bloc`, and `go_router`.
- **Existing test baseline:** `flutter test` currently fails 2 Auth BLoC tests because `test/presentation/blocs/auth/auth_bloc_test.dart` still verifies `SubscribeFcmTopicsUseCase.executeSubscribe(...)`, but `AuthBloc` now only receives `LoginUseCase` and no longer subscribes topics. Passing tests before expansion should start by aligning those expectations with the current Auth design.
- **Major modules/features found:** Auth, Splash, Home/Dashboard/FT Messaging, Valet, Location Check-In, Manual Check-In, Event Check-In, Food Ordering, Golf Bag Request, Inventory Stock Count, Lesson Pro, Member Photos, Member Search, Photo Gallery/Create Album, Push/Web Notification, Starter & Timesheet, MQTT, Remote API/Network, Local Storage/Platform Services, Shared Data Models/Mappers, Shared Widgets, Utilities, Routing, Dependency Injection, App Bootstrap.

<a id="2-testing-scope-inventory"></a>

## 2. Testing Scope Inventory
Legend: ✅ Unit Testable = can be tested now with mocks/fakes; ⚠️ Needs Minor Refactor = a small seam is needed; ❌ Needs Major Refactor = static/global/plugin coupling prevents isolated tests; ⏭️ Skip for Now = barrel/generated/constants/UI leaf to cover through parent flows.
### Auth
- `lib/domain/use_cases/auth/auth.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/auth/generate_token_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/auth/login_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/auth/logout_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/auth/refresh_token_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/auth/subscribe_fcm_topics_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/auth/auth_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/auth/auth_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/auth/auth_state.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — time/timer dependency
- `lib/presentation/screens/auth/auth.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/auth/login_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup
- `lib/presentation/screens/auth/widget/login_form_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/auth/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/auth/auth.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/auth/auth_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/auth/auth_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/auth/auth_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### App Bootstrap / Flavors
- `lib/firebase_options.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/main.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — Firebase bootstrap/static plugin
- `lib/main_dev.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/main_prod.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/main_staging.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
### Configuration
- `lib/config/app_config.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — platform singleton
- `lib/config/config.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Core
- `lib/core/api_result.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/core/core.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/core/errors/errors.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/core/network/http_status_code.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/core/network/network.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/core/network/network_state_service.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — time/timer dependency
- `lib/core/network/network_ui_actions.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
### Data Core
- `lib/data/data.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/data_source.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/data/data_source_impl.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/data/header.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/data/services/firebase_crash_reporter.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/data/services/services.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Dependency Injection
- `lib/di/di.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/di/injection.config.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/di/injection.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — global singleton/static runtime state
- `lib/di/injection_utils.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
- `lib/di/moduls/api_service_injection.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
- `lib/di/moduls/app_module_injection.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — Firebase bootstrap/static plugin
- `lib/di/moduls/bloc_injection.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
- `lib/di/moduls/modules.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/di/moduls/repository_injection.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
- `lib/di/moduls/use_case_injection.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
### Domain Core
- `lib/domain/domain.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/services/crash_reporter.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/domain/services/services.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Domain Use Cases
- `lib/domain/use_cases/base_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/local_data_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/use_cases.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Event Check-In
- `lib/domain/use_cases/event_check_in/add_walk_on_members_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/event_check_in/delete_walk_on_members_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/event_check_in/event_check_in_member_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/event_check_in/event_check_in_use_case.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/event_check_in/get_registered_member_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/event_check_in/get_walk_on_member_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/event_check_in/event_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/event_check_in/event_check_in_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — time/timer dependency
- `lib/presentation/blocs/event_check_in/event_check_in_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/event_check_in/event_check_in_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/event_check_in/event_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/event_check_in/event_check_in_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/event_check_in/widget/activity_select_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/add_walk_member_tile_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/add_walk_members_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/event_check_in/widget/check_in_header_button_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/event_check_in/widget/event_check_in_body_header_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/event_check_in_memeber_grid_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/event_check_in/widget/event_check_in_tab_content_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/event_check_in_tab_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/event_fiter_header_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; time/timer dependency
- `lib/presentation/screens/event_check_in/widget/header_filter_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/member_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/walk_member_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/walk_on_member_grid_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/event_check_in/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/event_check_in/event_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/event_check_in/event_check_in_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/event_check_in/event_check_in_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/event_check_in/event_check_in_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Food Ordering
- `lib/domain/use_cases/food_ordering/food_ordering.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/food_ordering/get_food_locations_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/food_ordering/get_food_order_details_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/food_ordering/get_food_order_history_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/food_ordering/get_food_orders_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/food_ordering/get_member_order_history_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/food_ordering/update_food_order_status_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/food_ordering/food_ordering.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/food_ordering/food_ordering_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/food_ordering_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/food_ordering_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/order_details/order_details.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/food_ordering/order_details/order_details_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — service locator/global DI lookup; time/timer dependency
- `lib/presentation/blocs/food_ordering/order_details/order_details_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/order_details/order_details_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/order_history/order_history.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/food_ordering/order_history/order_history_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/order_history/order_history_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/food_ordering/order_history/order_history_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/food_ordering/food_ordering.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/food_ordering/food_ordering_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/food_ordering/pages/delivery_body_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/pages/order_details_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/food_ordering/pages/order_history_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/food_ordering/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/food_ordering/pages/pickup_order_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/cancelation_reason_popup_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/food_ordering/widget/choose_type_popup_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/food_ordering/widget/eta_time_selection_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/food_order_responsive_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/food_ordering_tabbar_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/item_details_with_bills_breakdown.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/member_order_history_mobile.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/food_ordering/widget/member_order_history_tab_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/memeber_order_history_card.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_card_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_details_buttons_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/food_ordering/widget/order_details_mobile_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_details_tablet_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_details_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/order_due_at_minutes_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/order_history_card_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_history_details_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup; time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_history_header_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_status_buttons_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/screens/food_ordering/widget/order_tile_with_border_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/food_ordering/widget/ripple_animated_button_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/food_ordering/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/food_ordering/food_ordering.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/food_ordering/food_ordering_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/food_ordering/food_ordering_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/food_ordering/food_ordering_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Golf Bag Request
- `lib/domain/use_cases/golf_bag_request/get_golf_bag_request_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/golf_bag_request/get_golf_location_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/golf_bag_request/golf_bag_request.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/golf_bag_request/update_golf_bag_order_status_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/golf_bag_request/golf_bag_request.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/golf_bag_request/golf_bag_request_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/blocs/golf_bag_request/golf_bag_request_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/golf_bag_request/golf_bag_request_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/golf_bag_request/golf_history/golf_history_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/golf_bag_request/golf_history/golf_history_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/golf_bag_request/golf_history/golf_history_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/golf_bag_request/golf_bag_history/golf_bag_history.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/golf_bag_request/golf_bag_history/golf_bag_request_history_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/golf_bag_request/golf_bag_history/request_history_details_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/golf_bag_request_history_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/golf_history_empty_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/golf_history_error_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/golf_history_tile.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/golf_bag_request/golf_bag_request.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/golf_bag_request/golf_bag_request_page_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/widgets/bag_request_details_dialog.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/widgets/decline_reason_dialog.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/widgets/golf_order_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/golf_bag_request/widgets/golf_request_card_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/golf_bag_request/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/golf_bag_request/golf_bag_request.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/golf_bag_request/golf_bag_request_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/golf_bag_request/golf_bag_request_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/golf_bag_request/golf_bag_request_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Home / Dashboard / FT Messaging Entry
- `lib/domain/use_cases/home/build_access_list_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/home/build_side_menu_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/home/get_app_update_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/home/home.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/home/home_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — global navigator/dialog dependency; plugin singleton/static SDK; service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/blocs/home/home_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/home/home_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/home/home.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/home/home_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/home/widget/dashboad_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup
- `lib/presentation/screens/home/widget/dashboard_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/home/widget/home_drawer_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; static Utility/UI/platform helper
- `lib/presentation/screens/home/widget/menu_items_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/home/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/home/home.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/home/home_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/home/home_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/home/home_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Integrations
- `lib/integration/integration.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
### Inventory Stock Count
- `lib/domain/use_cases/inventory_stock_count/get_count_sheet_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/inventory_stock_count/get_inventory_count_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/inventory_stock_count/get_inventory_details_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/inventory_stock_count/inventory_stock_count.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/inventory_stock_count/update_count_sheet_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/inventory_stock_count/inventory_stock_count.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/inventory_stock_count/inventory_stock_count_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/inventory_stock_count/inventory_stock_count_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/inventory_stock_count/inventory_stock_count_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/inventory_stock_count/inventory_stock_count.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/inventory_stock_count/inventory_stock_count_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/inventory_stock_count/pages/manage_stock_count_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/screens/inventory_stock_count/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/inventory_stock_count/pages/scan_qr_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup
- `lib/presentation/screens/inventory_stock_count/pages/stock_count_details_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/inventory_stock_count/widget/not_counted_page_body_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/inventory_stock_count/widget/search_page_body_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/inventory_stock_count/widget/stock_count_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/inventory_stock_count/widget/stock_count_change_field_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/inventory_stock_count/widget/stock_count_details_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/inventory_stock_count/widget/stock_details_tab_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/inventory_stock_count/widget/stock_manage_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/inventory_stock_count/widget/stock_qr_scanner_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/screens/inventory_stock_count/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/inventory_stock_count/inventory_stock_count.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/inventory_stock_count/inventory_stock_count_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/inventory_stock_count/inventory_stock_count_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/inventory_stock_count/inventory_stock_count_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Lesson Pro
- `lib/domain/use_cases/lesson_pro/block_date_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/block_lesson_book_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/delete_blocked_date_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/get_lesson_details_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/get_lesson_focus_day_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/get_lesson_month_data_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/get_lesson_types_by_lesson_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/get_lesson_types_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/lesson_pro/lesson_pro.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/lesson_pro/post_lesson_details_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/lesson_pro/lesson_pro.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/lesson_pro/lesson_pro_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/lesson_pro/lesson_pro_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/lesson_pro/lesson_pro_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/lesson_pro/lesson_pro.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/lesson_pro/lesson_pro_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; time/timer dependency
- `lib/presentation/screens/lesson_pro/widget/add_update_reservation_bottom_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/add_update_reservation_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/lesson_pro/widget/bottom_notes_and_checkbox_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/calendar_header_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/calendar_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/form_field_with_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/info_dialog_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_body.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_bottom_nav_bar_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_calendar_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_desktop_content.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_loaded_content.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_loading_content.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_pro_mobile_content.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/lesson_time_item_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/screens/lesson_pro/widget/lesson_type_overlly_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/select_lession_type_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/user_photo_and_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/view_day_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/lesson_pro/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/lesson_pro/lesson_pro.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/lesson_pro/lesson_pro_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/lesson_pro/lesson_pro_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/lesson_pro/lesson_pro_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Local Storage / Platform Services
- `lib/data/local/local.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/local/local_storage_keys.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/app_badge_service.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/audio_player_service_manager.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — platform singleton
- `lib/data/local/managers/audio_player_web_service_manager.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/audio_player_web_service_manager_stub.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/audio_player_web_service_manager_web.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/device_info_channel.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/device_info_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/download_media_service.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — platform singleton; time/timer dependency
- `lib/data/local/managers/firebase_notification_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/image_cropper_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/image_picker_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/local_storage_manager.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/managers.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/local/managers/mqtt_client_platform_io.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/mqtt_client_platform_web.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/mqtt_service_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/session_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — global singleton/static runtime state
- `lib/data/local/managers/shared_preferences_manager.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/starter_url_builder.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/managers/upload_image_service.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/managers/user_default_data_migration_manager.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — platform singleton; service locator/global DI lookup
### Location Check-In
- `lib/domain/use_cases/location_check_in/do_manual_location_check_in_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/get_check_in_locations_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/get_checked_in_members_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/get_family_members_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/get_location_check_in_user_details_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/location_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/location_check_in/location_check_out_user_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/location_void_check_in_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/location_check_in/validate_qr_code_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/location_check_in/location_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/location_check_in/location_check_in_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — global navigator/dialog dependency; service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/blocs/location_check_in/location_check_in_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/location_check_in/location_check_in_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/location_check_in/location_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/location_check_in/location_check_in_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/pages/manual_check_in_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/location_check_in/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/location_check_in/widgets/add_button_tile_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/add_family_memebers_popup_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/add_guest_popup_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/checked_in_memeber_card_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/location_check_in/widgets/family_member_card_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/family_members_section_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/location_check_in/widgets/guest_dropdown_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/guest_user_tile_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/guests_section_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/location_check_in/widgets/keyboard_wedge_listener.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/location_check_in_tab_buttons.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/location_check_in/widgets/location_chek_in_content.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/manual_check_in_actions_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/manual_check_in_user_card_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/location_check_in/widgets/qr_code_scanner_popup_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/location_check_in/widgets/qr_code_scanner_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton; service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/screens/location_check_in/widgets/rentals_section_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/rented_items_tile_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/visible_counter_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/location_check_in/widgets/void_check_in_popup.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/presentation/screens/location_check_in/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/location_check_in/location_check_in.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/location_check_in/location_check_in_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/location_check_in/location_check_in_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/location_check_in/location_check_in_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### MQTT
- `lib/presentation/blocs/mqtt/mqtt_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — service locator/global DI lookup; time/timer dependency
- `lib/presentation/blocs/mqtt/mqtt_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/mqtt/mqtt_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
### Manual Check-In
- `lib/presentation/blocs/manual_check_in/manual_check_in_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — static Utility/UI/platform helper
- `lib/presentation/blocs/manual_check_in/manual_check_in_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/manual_check_in/manual_check_in_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
### Member Photos
- `lib/domain/use_cases/member_photo/get_member_photo_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/member_photo/get_members_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/member_photo/member_photo.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/member_photo/update_member_photo_status_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/member_photos/member_photos.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/member_photos/member_photos_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — global navigator/dialog dependency; static media/file/camera dependency
- `lib/presentation/blocs/member_photos/member_photos_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/member_photos/member_photos_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/member_photos/member_photos.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/member_photos/member_photos_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/member_photos/pages/add_members_photo_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/member_photos/pages/member_photo_viewer_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/member_photos/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/member_photos/widgets/member_photo_item.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/member_photos/widgets/mobile_photo_picker_options.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/member_photos/widgets/photo_view_bottom_bar_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/member_photos/widgets/tablet_photo_picker_options.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/member_photos/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/member_photos/member_photos.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/member_photos/member_photos_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/member_photos/member_photos_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/member_photos/member_photos_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Member Search
- `lib/presentation/cubits/member_search/member_search.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/cubits/member_search/member_search_cubit.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/cubits/member_search/member_search_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/member_search/member_search.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/member_search/member_search_content_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/member_search/member_search_page_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
### Photo Gallery / Create Album
- `lib/domain/use_cases/create_album/create_album.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/create_album/create_album_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/create_album/delete_album_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/create_album/update_album_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/delete_photo_from_album_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/get_my_photos_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/get_photo_gallery_albums_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/get_photos_by_album_id_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/photo_gallery.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/photo_gallery/update_photo_gallery_image_status_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/photo_gallery/upload_photos_on_album_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/photo_gallery/create_album/create_album.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/photo_gallery/create_album/create_album_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — static media/file/camera dependency
- `lib/presentation/blocs/photo_gallery/create_album/create_album_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/photo_gallery/create_album/create_album_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/photo_gallery/photo_gallery.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/photo_gallery/photo_gallery_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — static media/file/camera dependency
- `lib/presentation/blocs/photo_gallery/photo_gallery_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/photo_gallery/photo_gallery_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/photo_gallery/create_album/create_album.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/photo_gallery/create_album/create_album_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/screens/photo_gallery/create_album/widgets/album_date_picker.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton; time/timer dependency
- `lib/presentation/screens/photo_gallery/create_album/widgets/album_details_form.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/album_show_configration.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/choose_cover_image_from_album.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/cover_photo_card.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/photo_gallery/create_album/widgets/create_album_save_action.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/danger_zone_card.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/display_in_app_configration.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/manage_photos_card.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/create_album/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/photo_gallery/pages/album_details_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/photo_gallery/pages/album_image_preview.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/photo_gallery/pages/album_manage_photos_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/photo_gallery/pages/album_pending_review_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/photo_gallery/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/photo_gallery/pages/pending_photo_preview.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/presentation/screens/photo_gallery/photo_gallery.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/photo_gallery/photo_gallery_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton; service locator/global DI lookup; time/timer dependency
- `lib/presentation/screens/photo_gallery/widgets/album_details_card.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/widgets/pending_apprvoval_card.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/photo_gallery/widgets/photo_album_card.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/photo_gallery/widgets/photo_album_options_sheet.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton
- `lib/presentation/screens/photo_gallery/widgets/review_pending_card.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/photo_gallery/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/photo_gallery/widgets/zoomable_image_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/remote/services/create_album/create_album.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/create_album/create_album_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/create_album/create_album_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/create_album/create_album_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/photo_gallery/photo_gallery.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/photo_gallery/photo_gallery_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/photo_gallery/photo_gallery_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/photo_gallery/photo_gallery_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Presentation Shell
- `lib/presentation/blocs/blocs.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/observer.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/cubits/app_session/app_session.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/cubits/app_session/app_session_cubit.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/cubits/cubits.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/mixins/location_check_in_view_state_mixin.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency
- `lib/presentation/network/app_network_ui_actions.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency
- `lib/presentation/network/network.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/presentation.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/screens.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Push / Web Notification
- `lib/data/local/notifications/firebase_notification_background.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — Firebase bootstrap/static plugin
- `lib/data/local/notifications/local_notification_service.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — platform singleton
- `lib/data/local/notifications/notification_config.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/notifications/notification_constants.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/local/notifications/notification_data_extractor.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/notifications/notification_interfaces.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/notifications/notification_payload_handler.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup
- `lib/data/local/notifications/notification_permission_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/notifications/notification_topic_manager.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/data/local/notifications/notifications.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/local/notifications/web_local_notification_manager.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/notifications/web_local_notification_manager_stub.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/data/local/notifications/web_local_notification_manager_web.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — time/timer dependency
- `lib/domain/use_cases/push_notification/delete_push_notification_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_distribution_list_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_event_activity_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_event_list_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_member_groups_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_notification_list_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/get_recipients_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/push_notification/push_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/push_notification/send_push_notification_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/web_notification/subscribe_to_topic_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/web_notification/unsubscribe_from_topic_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/web_notification/web_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/push_notification/push_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/push_notification/push_notification_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — static Utility/UI/platform helper
- `lib/presentation/blocs/push_notification/push_notification_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/push_notification/push_notification_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/push_notification/pages/add_members_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/pages/create_notification_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency
- `lib/presentation/screens/push_notification/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/push_notification/pages/recipient_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/presentation/screens/push_notification/push_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/push_notification/push_notification_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/push_notification/recipient_tab_views/not_viewed_tab_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/recipient_tab_views/recipient_tab_views.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/push_notification/recipient_tab_views/sent_to_tab_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/recipient_tab_views/viewed_tab_view.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/widgets/all_members_chip.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/widgets/dropdown_item.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/widgets/notification_dynamic_dropdowns.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; time/timer dependency
- `lib/presentation/screens/push_notification/widgets/push_notification_item.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/push_notification/widgets/recipient_section.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/widgets/selected_members_chips.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/push_notification/widgets/send_resend_notification_action_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper
- `lib/presentation/screens/push_notification/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/push_notification/push_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/push_notification/push_notification_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/push_notification/push_notification_service_end_point.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/push_notification/push_notification_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/web_notification/web_notification.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/web_notification/web_notification_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/web_notification/web_notification_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/web_notification/web_notification_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Remote API / Network
- `lib/remote/network/network.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/network/network_client.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — global singleton/static runtime state
- `lib/remote/remote.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/base_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/services.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Repositories
- `lib/data/repositories/auth_repositories_impl.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — platform singleton; service locator/global DI lookup; static Utility/UI/platform helper
- `lib/data/repositories/create_album_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/event_check_in_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/food_order_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/golf_bag_request_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/home_repositories_impl.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — service locator/global DI lookup
- `lib/data/repositories/inventory_stock_count_repositories_impl.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — service locator/global DI lookup
- `lib/data/repositories/lesson_pro_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/local_storage_repository_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/location_check_in_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/memeber_photo_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/photo_gallery_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/push_notification_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/repositories.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/repositories/starter_and_timesheet_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/valet_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/data/repositories/web_notification_repositories_impl.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/domain/repositories/auth_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/base_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/create_album_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/event_check_in_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/food_order_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/golf_bag_request_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/home_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/inventory_stock_count_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/lesson_pro_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/local_storage_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/location_check_in_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/member_photo_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/photo_gallery_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/push_notification_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/repositories.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/repositories/starter_and_timesheet_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/valet_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
- `lib/domain/repositories/web_notification_repositories.dart` — ⏭️ Skip for Now — Recommended: None — abstract contract only
### Resources / Theme
- `lib/res/constants/app_constants.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/constants/asset_constants.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/constants/constants.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/constants/order_constants.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/dimens.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/res.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/strings/strings.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/strings/translation_file.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/strings/translation_util.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/theme/app_theme.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/theme/colors.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/theme/styles.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/res/theme/theme.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Routing
- `lib/utils/navigator/app_router.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — global singleton/static runtime state
- `lib/utils/navigator/app_routes.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/navigator/navigation_service.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/navigator/navigation_service_impl.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/navigator/navigation_utils.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency
- `lib/utils/navigator/navigator.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/utils/navigator/route_argument_parser.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/navigator/route_management.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency
### Shared Data Models
- `lib/data/models/check_in_family_members_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/check_in_location_qr_response.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/check_in_location_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/check_in_members_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/check_in_user_details_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/common_meta_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/course_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/custom_home_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/custom_media_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/custom_response.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/event_check_in_member_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/event_check_in_request_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/event_check_in_walk_member_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/fcm_notification_data.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/food_location_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/food_order_details_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/food_order_history_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/food_order_response_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/generate_token_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_activity_timeline.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_member_data.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_order_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_orders_request_response_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_orders_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_request_golf_bag.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_request_location_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/golf_selected_member.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/inventory_count_sheet_item_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/inventory_stock_count_request_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/inventory_stock_count_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_focus_day_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_member_search_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_pro_details_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_pro_lesson_types_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_pro_month_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/lesson_pro_request_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/locations_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/login_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/login_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/member_photo_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/members_response_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/models.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/models/mqtt_check_in_payload_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/mqtt_event_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/mqtt_golf_bag_payload_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/mqtt_order_payload_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/mqtt_valet_payload_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/pagination_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/photo_gallery_album_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/photo_gallery_create_album_request.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/photo_gallery_photo_album_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_activity_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_distribution_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_events_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_member_group_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_recipients_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_request_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/push_notification_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/refresh_token_reponse_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/starter_activity_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/upload_medial_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/user_access_data_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/valet_check_in_user_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/valet_check_in_users_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
- `lib/data/models/validate_qr_code_response_model.dart` — ✅ Unit Testable — Recommended: Unit — JSON/equality/value object behavior
### Shared Mappers
- `lib/data/mappers/auth_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/common_response_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/event_check_in_mapper.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/food_ordering_mapper.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/golf_bag_request_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/inventory_stock_count_mapper.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/lesson_pro_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/location_check_in_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/mappers.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/data/mappers/member_photo_mapper.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/photo_gallery_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/push_notification_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/starter_and_timesheet_mapper.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/data/mappers/valet_mappers.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
### Shared Widgets
- `lib/widgets/animated_toggle_slider_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/app_button_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/app_image.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/widgets/app_loader_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/app_ticker.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
- `lib/widgets/attach_image_picker_sheet.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton
- `lib/widgets/calendar_controller.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/widgets/change_location_popup_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/comming_soon_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
- `lib/widgets/custom_appbar_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/custom_calendar_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/widgets/custom_check_box.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/custom_date_picker_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; time/timer dependency
- `lib/widgets/custom_dropdown_field.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/custom_shimmer.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/custom_textfield.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/custom_webview.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; static Utility/UI/platform helper
- `lib/widgets/download_progress_dialog.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency
- `lib/widgets/empty_data_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/form_field_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/image_place_holder.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/qr_corner_bracket_overlay.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/refreshable_scroll_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/widgets/show_image_picker_bottom_sheet.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton
- `lib/widgets/simple_image_placeholder.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/tap_handler.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/web_image_element.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/widgets/web_image_element_stub.dart` — ✅ Unit Testable — Recommended: Unit — plain Dart logic
- `lib/widgets/web_image_element_web.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/wheel_time_picker_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/widgets/widgets.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/widgets/windows_camera/windows_camera.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; static media/file/camera dependency
- `lib/widgets/windows_camera/windows_camera_capture_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; static Utility/UI/platform helper; static media/file/camera dependency
- `lib/widgets/windows_camera/windows_camera_session.dart` — ❌ Needs Major Refactor — Recommended: Unit/Integration — plugin/bootstrap/global singleton
### Splash
- `lib/presentation/blocs/splash/splash_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/splash/splash_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/splash/splash_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/splash/splash.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/splash/splash_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup
### Starter & Timesheet
- `lib/domain/use_cases/starter_and_timesheet/get_starter_club_name_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/starter_and_timesheet/get_starter_url_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/starter_and_timesheet/starter_and_timesheet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/starter_and_timesheet/starter_and_timesheet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/starter_and_timesheet/starter_and_timesheet_bloc.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/starter_and_timesheet/starter_and_timesheet_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/starter_and_timesheet/starter_and_timesheet_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/starter/starter.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/starter/starter_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — service locator/global DI lookup; static Utility/UI/platform helper
- `lib/remote/services/starter_and_timesheet/starter_and_timesheet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/starter_and_timesheet/starter_and_timesheet_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/starter_and_timesheet/starter_and_timesheet_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/starter_and_timesheet/starter_and_timesheet_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
### Utilities
- `lib/utils/app_locale_delegate.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/app_mixin.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/date_time_extensions.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — service locator/global DI lookup; time/timer dependency
- `lib/utils/date_time_utils.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — time/timer dependency
- `lib/utils/debouncer.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/dialog_utils.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency; platform singleton
- `lib/utils/download_and_share_image_helper.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency; platform singleton; static media/file/camera dependency; time/timer dependency
- `lib/utils/enums.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/error_handler.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/exceptions.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/extensions.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency
- `lib/utils/formatters/formatters.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/utils/formatters/name_input_formatter.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/helpers/helpers.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/utils/helpers/lesson_pro_block_day_helper.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/helpers/lesson_pro_drag_intent.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/helpers/location_check_in_helpers.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/language.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/log.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/translate.dart` — ✅ Unit Testable — Recommended: Unit — helper/service behavior
- `lib/utils/utility.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit/Widget — global navigator/dialog dependency; platform singleton; time/timer dependency
- `lib/utils/utils.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
### Valet
- `lib/domain/use_cases/valet/get_valet_checkin_users_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/valet/valet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/domain/use_cases/valet/valet_check_in_user_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/valet/valet_check_out_request_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/domain/use_cases/valet/valet_check_out_user_use_case.dart` — ✅ Unit Testable — Recommended: Unit — pure/domain wrapper logic
- `lib/presentation/blocs/valet/valet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/blocs/valet/valet_bloc.dart` — ⚠️ Needs Minor Refactor — Recommended: Unit — global navigator/dialog dependency; static Utility/UI/platform helper; static media/file/camera dependency
- `lib/presentation/blocs/valet/valet_event.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/blocs/valet/valet_state.dart` — ✅ Unit Testable — Recommended: Unit — constructor dependencies/state logic
- `lib/presentation/screens/valet/pages/pages.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/valet/pages/parking_request_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/pages/request_page.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/pages/valet_check_in_user_details_page.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; platform singleton
- `lib/presentation/screens/valet/valet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/presentation/screens/valet/valet_page_view.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — platform singleton; service locator/global DI lookup; time/timer dependency
- `lib/presentation/screens/valet/widget/check_in_image_collection_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/check_in_user_details_form_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/parking_request_tile_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/parking_request_user_card.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — time/timer dependency
- `lib/presentation/screens/valet/widget/parking_tab_header_button_widget.dart` — ⚠️ Needs Minor Refactor — Recommended: Widget — global navigator/dialog dependency; service locator/global DI lookup
- `lib/presentation/screens/valet/widget/real_time_count_down_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/request_checkout_with_time_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/valet_tabbar_widget.dart` — ⏭️ Skip for Now — Recommended: Widget — UI-only leaf/composition; test through parent flow first
- `lib/presentation/screens/valet/widget/widget.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/valet/valet.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/valet/valet_api_end_points.dart` — ⏭️ Skip for Now — Recommended: None — barrel/config/constants/generated or visual resource
- `lib/remote/services/valet/valet_api_service.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable
- `lib/remote/services/valet/valet_api_service_provider.dart` — ✅ Unit Testable — Recommended: Unit — dependencies injectable/mappable

<a id="3-tightly-coupled-code-identified-blockers"></a>

## 3. Tightly Coupled Code — Identified Blockers
| File | What test CANNOT be written today? | What dependency/coupling is blocking it? | Smallest refactor to unblock testing | Risk this refactor introduces | Suggested Priority |
|------|--------------------------------------|------------------------------------------|--------------------------------------|-------------------------------|-------------------|
| lib/presentation/blocs/auth/auth_state.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/auth/login_page_view.dart | Widget tests cannot supply a bloc or route manager without resetting global GetIt. | Widget creates `AuthBloc(InjectionUtils.getUseCase<LoginUseCase>())` and CTA calls `InjectionUtils.getRouteManagement()`. | Add optional `AuthBloc`/factory or use surrounding provider; inject CTA callback/route management. | Low; widget construction path changes only. | P0 Auth |
| lib/presentation/screens/auth/widget/login_form_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/main.dart | Bootstrap tests cannot run without Firebase/window/platform plugins. | Calls Firebase, Crashlytics, Messaging background handler, window manager, DeviceInfoChannel, SharedPreferences init, GetIt. | Split startup into injectable bootstrap services and keep flavor entry points thin. | High; app launch across platforms. | P3 |
| lib/config/app_config.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/core/network/network_state_service.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/di/injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global singleton/static runtime state | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/di/injection_utils.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/di/moduls/api_service_injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/di/moduls/app_module_injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | Firebase bootstrap/static plugin | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/di/moduls/bloc_injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/di/moduls/repository_injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/di/moduls/use_case_injection.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/event_check_in/event_check_in_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/event_check_in/event_check_in_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/event_check_in/widget/add_walk_members_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/event_check_in/widget/check_in_header_button_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/event_check_in/widget/event_check_in_memeber_grid_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/event_check_in/widget/event_fiter_header_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/food_ordering/order_details/order_details_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/food_ordering_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/pages/order_details_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/pages/order_history_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/cancelation_reason_popup_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/choose_type_popup_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/eta_time_selection_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/member_order_history_mobile.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/memeber_order_history_card.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_card_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_details_buttons_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_details_mobile_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_details_tablet_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_history_card_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_history_details_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_history_header_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/order_status_buttons_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/food_ordering/widget/ripple_animated_button_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/golf_bag_request/golf_bag_request_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | static Utility/UI/platform helper; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/golf_bag_request/golf_bag_history/golf_bag_request_history_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/golf_bag_request/golf_bag_history/request_history_details_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/golf_bag_request/golf_bag_history/widgets/golf_bag_request_history_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/golf_bag_request/widgets/golf_request_card_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/home/home_bloc.dart | Home navigation, app update dialog, FT Messaging launch, and badge update tests cannot isolate side effects. | Calls `InjectionUtils.getBloc`, `kNavigatorKey`, `DialogUtils`, `Utility`, `AppBadgeService.instance`, `FTMessaging`, `appConfig`. | Extract navigation/plugin/badge/update-dialog adapters; inject Food/Golf bloc lookup callbacks. | Medium; dashboard navigation is broad. | P2 |
| lib/presentation/screens/home/home_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/home/widget/dashboad_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/home/widget/home_drawer_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/inventory_stock_count_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/pages/manage_stock_count_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/pages/scan_qr_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/pages/stock_count_details_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/widget/not_counted_page_body_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/widget/search_page_body_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/inventory_stock_count/widget/stock_qr_scanner_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/lesson_pro/lesson_pro_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/lesson_pro/widget/add_update_reservation_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/lesson_pro/widget/lesson_pro_bottom_nav_bar_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/lesson_pro/widget/lesson_time_item_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/managers/audio_player_service_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/managers/device_info_channel.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/managers/device_info_manager.dart | Device metadata tests cannot simulate platforms/plugin responses. | Owns `DeviceInfoPlugin`, reads `Platform`/`kIsWeb`, calls `Utility.getAppVersion`. | Inject platform descriptor, plugin facade, and app-version provider. | Low-medium; payload metadata may change. | P2 |
| lib/data/local/managers/download_media_service.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/managers/firebase_notification_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/managers/image_cropper_manager.dart | Media tests cannot run without real plugins/filesystem. | Static manager or singleton around image/crop/upload plugins. | Define injectable media service interface and fake implementation for tests. | Medium; media UX and paths can regress. | P2 |
| lib/data/local/managers/image_picker_manager.dart | Media tests cannot run without real plugins/filesystem. | Static manager or singleton around image/crop/upload plugins. | Define injectable media service interface and fake implementation for tests. | Medium; media UX and paths can regress. | P2 |
| lib/data/local/managers/mqtt_client_platform_io.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/managers/mqtt_service_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/managers/session_manager.dart | Unauthorized/session-expiry flow cannot be unit tested deterministically. | Singleton pulls BLoCs/use cases/route/FCM from GetIt, uses global navigator/dialogs, delays, cache clearing. | Create an instance constructor with injected dependencies and keep `instance` as production wrapper. | High; logout/session reset is cross-cutting. | P1 |
| lib/data/local/managers/upload_image_service.dart | Media tests cannot run without real plugins/filesystem. | Static manager or singleton around image/crop/upload plugins. | Define injectable media service interface and fake implementation for tests. | Medium; media UX and paths can regress. | P2 |
| lib/data/local/managers/user_default_data_migration_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/location_check_in/location_check_in_bloc.dart | QR scan tests cannot isolate club validation, toast side effects, dialog navigation, debounce refresh, or generated timestamps. | Uses `kGetIt<DataSource>()`, `Utility.showToastMessage`, `KeyboardWedgeFocus`, `Timer`, and `DateTime.now()`. | Inject `DataSource`, toast/focus adapter, clock, and debounce duration. | Medium; QR check-in flow is business critical. | P1 |
| lib/presentation/screens/location_check_in/pages/manual_check_in_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/checked_in_memeber_card_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/family_members_section_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/guests_section_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/location_check_in_tab_buttons.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/manual_check_in_user_card_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/qr_code_scanner_popup_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/qr_code_scanner_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton; service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/location_check_in/widgets/void_check_in_popup.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/mqtt/mqtt_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/manual_check_in/manual_check_in_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/member_photos/member_photos_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; static media/file/camera dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/member_photos/member_photos_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/member_photos/pages/add_members_photo_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/member_photos/widgets/mobile_photo_picker_options.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/member_photos/widgets/photo_view_bottom_bar_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/photo_gallery/create_album/create_album_bloc.dart | Image selection/upload tests cannot run without static image picker/cropper/upload services. | Static `ImagePickerManager`, `ImageCropperManager`, `ImageUploadService.instance`, `Utility.isFileSizeValid`, `File`. | Inject media service abstraction and file-size validator. | Medium; upload flows must remain stable. | P2 |
| lib/presentation/blocs/photo_gallery/photo_gallery_bloc.dart | Image selection/upload tests cannot run without static image picker/cropper/upload services. | Static `ImagePickerManager`, `ImageCropperManager`, `ImageUploadService.instance`, `Utility.isFileSizeValid`, `File`. | Inject media service abstraction and file-size validator. | Medium; upload flows must remain stable. | P2 |
| lib/presentation/screens/photo_gallery/create_album/create_album_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/create_album/widgets/album_date_picker.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/create_album/widgets/cover_photo_card.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/pages/album_details_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/pages/album_image_preview.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/pages/album_manage_photos_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/pages/album_pending_review_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/pages/pending_photo_preview.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/photo_gallery_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton; service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/widgets/photo_album_card.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/widgets/photo_album_options_sheet.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/photo_gallery/widgets/review_pending_card.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/mixins/location_check_in_view_state_mixin.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/network/app_network_ui_actions.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/notifications/firebase_notification_background.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | Firebase bootstrap/static plugin | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/notifications/local_notification_service.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/notifications/notification_payload_handler.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/local/notifications/notification_permission_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/notifications/notification_topic_manager.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/data/local/notifications/web_local_notification_manager_web.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/push_notification/push_notification_bloc.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/pages/create_notification_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/pages/recipient_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/push_notification_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/widgets/notification_dynamic_dropdowns.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/widgets/push_notification_item.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/push_notification/widgets/send_resend_notification_action_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/remote/network/network_client.dart | 401 refresh, no-internet loop, retry, and loader tests need global app/session state. | Reads `appConfig`, `Utility.isNetworkAvailable`, `kGetIt<LocalDataUseCase>()`, `SessionManager.instance`, static refresh future. | Inject network status, token provider, session handler, clock/delay, and base config. | High; all API traffic goes through this file. | P1 |
| lib/data/repositories/auth_repositories_impl.dart | Repository login/logout/refresh tests cannot fully stub device token, app version, FCM token, date/device payload, or loader behavior. | Fields resolve `kGetIt<DeviceInfoManager>()` and `kGetIt<FirebaseNotificationManager>()`; login also calls `Utility.withProgressDialog`. | Inject `DeviceInfoManager`, `FirebaseNotificationManager`, and a progress-dialog runner/clock through the constructor. | Constructor/injection registration changes can break login bootstrapping. | P0 Auth |
| lib/data/repositories/home_repositories_impl.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/data/repositories/inventory_stock_count_repositories_impl.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/navigator/app_router.dart | Redirect/error route decisions cannot be tested without global GetIt and static router state. | Static `GoRouter router` uses `InjectionUtils.getUseCase<LocalDataUseCase>()` inside redirect/errorBuilder. | Build router from a factory accepting auth/session dependencies. | Medium; route initialization changes. | P2 |
| lib/utils/navigator/navigation_utils.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/navigator/route_management.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/app_image.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/app_ticker.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/widgets/attach_image_picker_sheet.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/calendar_controller.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/comming_soon_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/custom_calendar_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/custom_date_picker_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/custom_webview.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/download_progress_dialog.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/refreshable_scroll_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/show_image_picker_bottom_sheet.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/windows_camera/windows_camera.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; static media/file/camera dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/windows_camera/windows_camera_capture_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; static Utility/UI/platform helper; static media/file/camera dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/widgets/windows_camera/windows_camera_session.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | plugin/bootstrap/global singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P3 |
| lib/presentation/screens/splash/splash_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/starter/starter_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; static Utility/UI/platform helper | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/date_time_extensions.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/date_time_utils.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/dialog_utils.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/download_and_share_image_helper.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton; static media/file/camera dependency; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/extensions.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/utils/utility.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/blocs/valet/valet_bloc.dart | Image-pick/upload and MQTT debounce branches cannot be tested without real static media services or real timers. | Uses `ImagePickerManager`, `ImageCropperManager`, `Utility.withProgressDialog`, `Timer`, platform `File`. | Inject media picker/cropper/uploader/progress and debounce duration. | Medium; valet image check-in is user-facing. | P2 |
| lib/presentation/screens/valet/pages/valet_check_in_user_details_page.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; platform singleton | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/valet/valet_page_view.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | platform singleton; service locator/global DI lookup; time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/valet/widget/parking_request_user_card.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | time/timer dependency | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |
| lib/presentation/screens/valet/widget/parking_tab_header_button_widget.dart | Unit/widget test cannot isolate this file without configuring global/plugin state. | global navigator/dialog dependency; service locator/global DI lookup | Inject the named dependency behind a constructor parameter or small adapter; keep production registration in DI. | Low-medium, depending on constructor/DI changes. | P2 |

<a id="4-refactoring-plan"></a>

## 4. Refactoring Plan (Testing-Only, No Cleanup)
Only the changes below are needed to unblock tests; they are not architectural cleanup recommendations.
### `lib/data/repositories/auth_repositories_impl.dart`
- **Current problem:** `deviceInfo` and `firebaseNotificationManager` are resolved as fields from `kGetIt`, and login wraps execution in `Utility.withProgressDialog`.
- **Exact change needed:** Accept `DeviceInfoManager`, `FirebaseNotificationManager`, optional `ProgressRunner`, and optional clock/date provider in the constructor. Production DI passes the current implementations; tests pass fakes.
- **Estimated effort:** Small (< 1hr)
### `lib/presentation/screens/auth/login_page_view.dart`
- **Current problem:** The page constructs `AuthBloc` with `InjectionUtils.getUseCase<LoginUseCase>()` and navigates with `InjectionUtils.getRouteManagement()`.
- **Exact change needed:** Let the widget use an existing `BlocProvider<AuthBloc>` when present or accept an optional `AuthBloc Function()`/route callback. Keep default production factory wired to DI.
- **Estimated effort:** Small (< 1hr)
### `lib/presentation/blocs/location_check_in/location_check_in_bloc.dart`
- **Current problem:** QR flow reads `kGetIt<DataSource>().getClubId()`, uses `Utility.showToastMessage`, `KeyboardWedgeFocus`, `DateTime.now()`, and hard-coded `Timer(800ms)`.
- **Exact change needed:** Inject `DataSource`, `ToastPort`, `FocusPort`, `Clock`, and debounce duration. Tests can then cover invalid QR, token validation, club mismatch, and MQTT refresh.
- **Estimated effort:** Medium (2–4hr)
### `lib/data/local/managers/session_manager.dart`
- **Current problem:** Singleton directly pulls use cases/BLoCs/FCM/navigation from GetIt and uses global navigator dialogs/delays.
- **Exact change needed:** Add an injectable constructor with dependencies and keep `SessionManager.instance` as the production singleton wrapper.
- **Estimated effort:** Large (1+ day)
### `lib/remote/network/network_client.dart`
- **Current problem:** Token refresh and unauthorized handling are tied to `kGetIt<LocalDataUseCase>()`, `SessionManager.instance`, `Utility.isNetworkAvailable`, global app config, and static refresh state.
- **Exact change needed:** Inject token store, session handler, network-status checker, delay function, and base config; leave HTTP `Client` injection as-is.
- **Estimated effort:** Large (1+ day)
### `lib/presentation/blocs/home/home_bloc.dart`
- **Current problem:** Dashboard navigation and FT Messaging use `InjectionUtils.getBloc`, `kNavigatorKey`, `DialogUtils`, `Utility`, `AppBadgeService.instance`, `FTMessaging`, and `appConfig`.
- **Exact change needed:** Inject dashboard side-effect ports: badge updater, app-update dialog/launcher, FT Messaging launcher, and bloc lookup callbacks for Food/Golf switch-location.
- **Estimated effort:** Medium (2–4hr)
### `lib/presentation/blocs/valet/valet_bloc.dart`
- **Current problem:** Image pick/crop/upload and progress wrapping use static managers and `Utility.withProgressDialog`; MQTT refresh uses real timers.
- **Exact change needed:** Inject media picker/cropper/uploader/progress runner and debounce duration.
- **Estimated effort:** Medium (2–4hr)
### `lib/presentation/blocs/photo_gallery/photo_gallery_bloc.dart` and `lib/presentation/blocs/photo_gallery/create_album/create_album_bloc.dart`
- **Current problem:** Image picking/cropping/uploading and file-size checks are static/plugin-backed.
- **Exact change needed:** Inject a `MediaAttachmentService` and `FileSizeValidator`.
- **Estimated effort:** Medium (2–4hr)
### `lib/utils/navigator/app_router.dart`
- **Current problem:** Static router performs auth redirects through `InjectionUtils.getUseCase<LocalDataUseCase>()`.
- **Exact change needed:** Expose `AppRouter.create({required AuthSessionReader sessionReader})`; production keeps `AppRouter.router = create(...)`.
- **Estimated effort:** Medium (2–4hr)
### `lib/main.dart`
- **Current problem:** `start` combines platform/Firebase/window/DI/cache setup with app widget startup.
- **Exact change needed:** Split bootstrap steps into injectable services and test each service with plugin fakes; keep flavor entry points thin.
- **Estimated effort:** Large (1+ day)
Example before/after for the Auth repository refactor:
```dart
// Before
final deviceInfo = kGetIt<DeviceInfoManager>();
final firebaseNotificationManager = kGetIt<FirebaseNotificationManager>();

// After
AuthApiRepositoryImpl(
  this._apiService,
  this._dataSource, {
  required DeviceInfoManager deviceInfo,
  required FirebaseNotificationManager firebaseNotificationManager,
  ProgressRunner progressRunner = Utility.withProgressDialog,
}) : _deviceInfo = deviceInfo,
     _firebaseNotificationManager = firebaseNotificationManager,
     _progressRunner = progressRunner;
```

<a id="5-refactoring-priority-order"></a>

## 5. Refactoring Priority Order
- **1. `lib/data/repositories/auth_repositories_impl.dart`** — Auth is the team-decided starting module and this file blocks repository-level login payload/storage tests.
- **2. `lib/presentation/screens/auth/login_page_view.dart`** — Small Auth refactor with immediate widget-test payoff and low behavioral risk.
- **3. `test/presentation/blocs/auth/auth_bloc_test.dart`** — Not production refactor, but required readiness fix: remove stale `SubscribeFcmTopicsUseCase` verifications or move topic assertions to Home/Subscribe use-case tests.
- **4. `lib/presentation/blocs/location_check_in/location_check_in_bloc.dart`** — High business value; QR and MQTT check-in behavior has many edge cases currently blocked by globals/timers.
- **5. `lib/remote/network/network_client.dart`** — Most shared code path. Refactor risk is higher, so do after Auth but before broad repository tests.
- **6. `lib/data/local/managers/session_manager.dart`** — Session expiry touches all modules and blocks reliable 401/logout tests.
- **7. `lib/presentation/blocs/home/home_bloc.dart`** — Dashboard routing/access list is high-value, but side-effect adapter work is moderate.
- **8. `lib/presentation/blocs/valet/valet_bloc.dart`** — Important module and partially testable now; media/progress adapters unlock image and confirm-parking tests.
- **9. `lib/presentation/blocs/photo_gallery/photo_gallery_bloc.dart` / `create_album_bloc.dart`** — Shared media abstraction will also help Valet and future upload flows.
- **10. `lib/utils/navigator/app_router.dart`** — Valuable for auth route guards, but widget/integration tests can start before router factory work.
- **11. `lib/main.dart`** — Large bootstrap split; defer until unit/widget layers are healthier.

<a id="6-unit-testing-plan"></a>

## 6. Unit Testing Plan — Module by Module
### Auth
- `lib/presentation/blocs/auth/auth_bloc.dart`
  - Functions/methods to test: AuthFormValidateEvent, AuthLoginEvent
  - Inputs: email/password strings; ApiResult<LoginResponseModel?>
  - Expected results: validation errors, loading/success/error states
  - Edge cases: empty email, invalid email, empty password, 400, thrown exception, null data
  - Mocks/stubs: Mock LoginUseCase
- `lib/domain/use_cases/auth/login_use_case.dart`
  - Functions/methods to test: executeLogin
  - Inputs: email/password/showLoader
  - Expected results: ApiResult success flag/status/data
  - Edge cases: repository throws, null response data
  - Mocks/stubs: Mock AuthRepository
- `lib/data/repositories/auth_repositories_impl.dart`
  - Functions/methods to test: login, logout, refreshToken, tokenGenerate
  - Inputs: credentials, showLoader, headers/tokens
  - Expected results: API payload, storage writes, mapped CustomResponse
  - Edge cases: FCM token null, API 401/400, storage failure
  - Mocks/stubs: Mock AuthApiService, DataSource, DeviceInfo, FirebaseNotificationManager after refactor
### Home / Dashboard / FT Messaging Entry
- `lib/domain/use_cases/home/build_access_list_use_case.dart`
  - Functions/methods to test: build
  - Inputs: LoginUserResponse access flags/counts
  - Expected results: expected HomeCustomData list and badges
  - Edge cases: all access false, starter activities, counts null
  - Mocks/stubs: none
- `lib/domain/use_cases/home/build_side_menu_use_case.dart`
  - Functions/methods to test: build
  - Inputs: accessList, selectedItem
  - Expected results: replacement/insertion/removal of menu items
  - Edge cases: food ordering, golf bag, null selected
  - Mocks/stubs: none
- `lib/presentation/blocs/home/home_bloc.dart`
  - Functions/methods to test: LoadAccessListEvent, HomeNavigationEvent, LoadAppUpdatesEvent, LogoutEvent
  - Inputs: fake login response/routes/API result
  - Expected results: access list states, navigation calls, badge update
  - Edge cases: missing login user, app update versions, logout failure
  - Mocks/stubs: Mock use cases, route manager; adapters needed for globals
### Location Check-In
- `lib/presentation/blocs/location_check_in/location_check_in_bloc.dart`
  - Functions/methods to test: location load/change, QR scan, check-in/out, MQTT update
  - Inputs: location ids, QR JSON, member ids, use case results
  - Expected results: loaded state, member list changes, navigation/toast callbacks
  - Edge cases: invalid JSON, club mismatch, token validation fail, checkout all
  - Mocks/stubs: Mock use cases, route manager; inject DataSource/toast/clock
### Valet
- `lib/presentation/blocs/valet/valet_bloc.dart`
  - Functions/methods to test: load parking/request lists, MQTT updates, remove images, confirm parking
  - Inputs: checkoutReq/search, MqttValetPayloadModel, image events
  - Expected results: loaded state list changes, uploaded image state, API calls
  - Edge cases: empty checkinId, stale silent refresh, crop cancel, checkout all
  - Mocks/stubs: Mock use cases, route manager; media/progress adapters
### Food Ordering
- `lib/presentation/blocs/food_ordering/food_ordering_bloc.dart`
  - Functions/methods to test: load locations/orders, switch location
  - Inputs: location id, showLoader, order results
  - Expected results: loaded orders/location states
  - Edge cases: all locations, empty orders, API failure
  - Mocks/stubs: Mock use cases/local data
- `lib/presentation/blocs/food_ordering/order_details/order_details_bloc.dart`
  - Functions/methods to test: load details/update status/member history
  - Inputs: order id/status/location
  - Expected results: details/history/status states
  - Edge cases: cancel/decline reason, API failure
  - Mocks/stubs: Mock food order use cases
### Event Check-In
- `lib/presentation/blocs/event_check_in/event_check_in_bloc.dart`
  - Functions/methods to test: event/activity/member load, walk-on add/delete, check-in
  - Inputs: event id/activity id/member payload
  - Expected results: registered/walk-on/member states
  - Edge cases: empty date, duplicate/failed walk-on, API failure
  - Mocks/stubs: Mock event and member use cases
### Golf Bag Request
- `lib/presentation/blocs/golf_bag_request/golf_bag_request_bloc.dart`
  - Functions/methods to test: load locations/orders, switch location, update status
  - Inputs: date range/location/status/order id
  - Expected results: order list and status update states
  - Edge cases: no locations, all locations, update failure
  - Mocks/stubs: Mock golf bag/local data use cases
- `lib/presentation/blocs/golf_bag_request/golf_history/golf_history_bloc.dart`
  - Functions/methods to test: history load
  - Inputs: date/search/location
  - Expected results: history state
  - Edge cases: empty/failure
  - Mocks/stubs: Mock get request use case
### Inventory Stock Count
- `lib/presentation/blocs/inventory_stock_count/inventory_stock_count_bloc.dart`
  - Functions/methods to test: load inventory/count sheet/details/update
  - Inputs: inventory id, count sheet id, item counts
  - Expected results: loaded/details/update result states
  - Edge cases: invalid count, empty list, API failure
  - Mocks/stubs: Mock inventory use cases
### Lesson Pro
- `lib/presentation/blocs/lesson_pro/lesson_pro_bloc.dart`
  - Functions/methods to test: month data, lesson types, block/delete/post lesson
  - Inputs: date, lesson id, request model
  - Expected results: calendar/type/detail/reservation states
  - Edge cases: blocked dates, unavailable types, API failure
  - Mocks/stubs: Mock lesson use cases
### Member Photos
- `lib/presentation/blocs/member_photos/member_photos_bloc.dart`
  - Functions/methods to test: load photos/members/update status
  - Inputs: member id/status/search
  - Expected results: photo list and status states
  - Edge cases: empty search, reject/approve failure
  - Mocks/stubs: Mock member photo use cases
### Member Search
- `lib/presentation/cubits/member_search/member_search_cubit.dart`
  - Functions/methods to test: search/load members
  - Inputs: query/type/page
  - Expected results: member result state
  - Edge cases: empty query, no results, failure
  - Mocks/stubs: Mock GetMembersUseCase
### Photo Gallery / Create Album
- `lib/presentation/blocs/photo_gallery/photo_gallery_bloc.dart`
  - Functions/methods to test: load albums/photos, upload, approve/reject/delete/select
  - Inputs: album id/status/page/images
  - Expected results: album/photo lists and selection state
  - Edge cases: pagination end, upload empty, delete fail
  - Mocks/stubs: Mock gallery use cases; media adapter for image picking
- `lib/presentation/blocs/photo_gallery/create_album/create_album_bloc.dart`
  - Functions/methods to test: cover image, create/update/delete/save, init edit
  - Inputs: album request, cover image source
  - Expected results: loaded form states and submitted callbacks
  - Edge cases: club logo missing, crop cancel, delete failure
  - Mocks/stubs: Mock use cases/local data; media adapter
### Push / Web Notification
- `lib/presentation/blocs/push_notification/push_notification_bloc.dart`
  - Functions/methods to test: load recipients/events/groups, send/delete notification
  - Inputs: recipient filters, request model
  - Expected results: lists, selected recipients, send/delete result
  - Edge cases: empty recipients, invalid filter, API failure
  - Mocks/stubs: Mock notification/member use cases
### Starter & Timesheet
- `lib/presentation/blocs/starter_and_timesheet/starter_and_timesheet_bloc.dart`
  - Functions/methods to test: get club name/url
  - Inputs: activity id
  - Expected results: starter URL and club state
  - Edge cases: missing credentials/activity
  - Mocks/stubs: Mock starter use cases
### MQTT
- `lib/presentation/blocs/mqtt/mqtt_bloc.dart`
  - Functions/methods to test: connect/subscribe/payload/disconnect
  - Inputs: topic, payload JSON
  - Expected results: connection/subscription states and downstream events
  - Edge cases: invalid payload, duplicate connect, permanent disconnect
  - Mocks/stubs: Mock LocalDataUseCase, DeviceInfoManager, MQTT service after refactor
### Core / Network / Local
- `lib/remote/network/network_client.dart`
  - Functions/methods to test: makeRequest, 401 refresh, no internet, errors
  - Inputs: request type/headers/payload/response
  - Expected results: ResponseModel and retry behavior
  - Edge cases: timeout, socket, 401 refresh fail, concurrent refresh
  - Mocks/stubs: Mock http Client; inject session/token/network adapters
- `lib/data/data_source_impl.dart`
  - Functions/methods to test: get headers/user fields
  - Inputs: stored login JSON/access token
  - Expected results: headers and field getters
  - Edge cases: malformed JSON, missing login
  - Mocks/stubs: Fake LocalStorageManager
- `lib/data/local/managers/shared_preferences_manager.dart`
  - Functions/methods to test: save/get/remove/clear
  - Inputs: key/value/data type
  - Expected results: stored values by type
  - Edge cases: null prefs before init, wrong type
  - Mocks/stubs: SharedPreferences.setMockInitialValues

<a id="7-integration-widget-testing"></a>

## 7. Integration & Widget Testing Opportunities
- **Widget tests that can begin in parallel now:** `LoginFormWidget`, Auth validation dialogs, Home access-list rendering with mocked `HomeBloc`, simple BLoC-driven screens that accept external providers, leaf cards such as food order cards, golf request cards, event/member cards, inventory stock cards, and photo album cards.
- **Widget tests needing minor refactor first:** `LoginPageView` DI construction, Location Check-In QR/manual check-in dialog flows, Home dashboard navigation/update dialogs, Photo Gallery/Create Album media pick flows, Valet image pick/check-in screens, stock QR scanner global navigation, and widgets that call `InjectionUtils` directly.
- **Integration/E2E flows:** cold start -> splash -> login -> dashboard; login failure/validation; dashboard access by permissions; food order location switch -> order details -> status update; location QR/manual check-in -> checkout; valet check-in with images -> checkout request -> checkout; event check-in registered/walk-on member; push notification create/send/resend/delete; photo gallery create album/upload/approve/delete; inventory scan/update count; session expiry 401 -> logout.
- **Parallelizable with unit testing:** Auth widget tests, pure use-case/mapper/model tests, BLoC tests where dependencies are constructor-injected, and integration smoke tests that use mocked/staging backends. Media picker, Firebase/session expiry, and router redirect tests should wait for their adapter refactors.

<a id="8-definition-of-done"></a>

## 8. Definition of Done — Per File
Use this matrix per file from the inventory. Each completed file should be logged with its exact path and the applicable row below.
| File classification | Minimum coverage expectation | Happy paths covered | Error/edge cases covered | Tests passing in CI | Peer reviewed |
|---|---:|---|---|---|---|
| BLoC/Cubit file | 85%+ line coverage or every public event/state transition covered | Yes | Yes: API failure, thrown exception, empty/null input, stale/debounce cases where applicable | Yes | Yes |
| Use case file | 90%+ line coverage | Yes | Yes: repository failure/exception/null data | Yes | Yes |
| Repository implementation | 80%+ line coverage after required DI seams | Yes | Yes: mapper failure, API failure, persistence failure, auth/headers edge cases | Yes | Yes |
| Mapper/model/helper | 90%+ line coverage | Yes | Yes: null/missing/malformed fields | Yes | Yes |
| Widget/screen selected for widget testing | Coverage is not primary; assert user-visible behavior and dispatched events | Yes | Yes: loading/empty/error/disabled states | Yes | Yes |
| Files marked ⏭️ Skip for Now | Not measured directly | Covered by parent flow where valuable | Covered by parent flow where valuable | Parent tests pass | Yes if touched |
Per-file DoD exceptions: `lib/main.dart`, `lib/utils/navigator/app_router.dart`, `lib/remote/network/network_client.dart`, and `lib/data/local/managers/session_manager.dart` should not be accepted on percentage alone; acceptance requires scenario coverage for bootstrap/redirect/retry/logout flows because branch behavior matters more than raw coverage.

<a id="9-progress-tracking-metrics"></a>

## 9. Progress Tracking Metrics

Instead of maintaining a separate Google Sheet/Google Doc for progress tracking, all testing and refactoring progress will be managed directly through Jira tickets.

### Jira-Based Tracking Approach

- Each module will have its own dedicated Jira ticket.
- Every ticket will contain structured checklist items for:
  - Refactoring tasks
  - Unit test implementation
  - Widget test implementation
  - Integration/smoke testing
  - Review status
  - CI validation

### Progress Tracking Rules

- Refactoring and testing must be tracked separately.
  - A task is not considered complete only because refactoring is done.
  - Testing completion requires:
    - Test cases added
    - CI passing
    - Code reviewed by another developer

- Developers should update checklist progress continuously within the Jira ticket.

- All testing-related PRs must be linked to the corresponding Jira ticket.

### Suggested Jira Checklist Structure

<ul>
  <li><input type="checkbox" disabled> Analyze module testability</li>
  <li><input type="checkbox" disabled> Identify required refactors</li>
  <li><input type="checkbox" disabled> Complete architecture cleanup/refactor</li>
  <li><input type="checkbox" disabled> Add unit tests</li>
  <li><input type="checkbox" disabled> Add widget tests</li>
  <li><input type="checkbox" disabled> Add integration/smoke tests (if required)</li>
  <li><input type="checkbox" disabled> Verify local test execution</li>
  <li><input type="checkbox" disabled> Peer code review completed</li>
  <li><input type="checkbox" disabled> QA/UAT validation completed</li>
  <li><input type="checkbox" disabled> Merge approved</li>
</ul>

### Weekly Tracking Metrics

The following metrics should be reviewed during weekly sync/review meetings:

- Modules completed this week
- Total cumulative modules completed
- Refactor tasks completed
- Pending blockers
- Approved blockers
- Current failing tests
- Module-wise test pass rate
- Coverage improvement by module
- Remaining high-risk modules/features

### Recommended Review Process

- Every testing PR should be reviewed by at least one other developer.
- Review should validate:
  - Test quality
  - Proper mocking strategy
  - Architecture improvements
  - No UI/business logic coupling
  - Stable and deterministic tests
  - CI stability

### Suggested Engineering Metrics

- BLoC events covered vs total BLoC events
- Use cases tested vs total use cases
- Repository implementations tested vs total repositories
- Widget flows covered vs planned widget flows
- Smoke/integration flows passing vs target flows
- Number of flaky tests detected
- Average PR review turnaround time
<a id="10-consistency-standards"></a>

## 10. Consistency Standards Across Developers
- Test folder structure should mirror `lib/`: `test/presentation/blocs/<module>/`, `test/presentation/screens/<module>/`, `test/domain/use_cases/<module>/`, `test/data/repositories/`, `test/data/mappers/`, `test/data/models/`, plus `test/helpers/`.
- Test files should be named `<source_file>_test.dart`, for example `auth_bloc_test.dart`, `login_use_case_test.dart`, `auth_repositories_impl_test.dart`.
- Test descriptions should use behavior language: `emits [Loading, Success] when login succeeds`, `returns invalid email validation state when email has no @`, `calls makeRequest with login endpoint and basic-auth headers`.
- Use Arrange/Act/Assert comments sparingly only when the setup is long. Prefer Given/When/Then structure in group names for complex flows.
- Every test file header should include a short comment with source file under test, test type, dependencies mocked, and any deliberate coverage exclusions.
- Shared mocks/fakes belong in `test/helpers/`: reusable mock use cases, fake response builders, `pumpWithScreenUtil`, `pumpWithBloc`, fake `RouteManagement`, fake storage, fake network client responses, and fixture builders for login/member/order/photo models.
- Prefer `mocktail` for consistency with current tests; use `bloc_test` for BLoC/Cubit behavior; avoid real `GetIt` except where a widget still requires it, and reset `kGetIt` in `tearDown`.

<a id="11-real-examples"></a>

## 11. Real Examples from THIS Codebase
### Example 1: Auth repository resolves hard dependencies from GetIt
Current code in `lib/data/repositories/auth_repositories_impl.dart`:
```dart
final deviceInfo = kGetIt<DeviceInfoManager>();
final firebaseNotificationManager = kGetIt<FirebaseNotificationManager>();

final deviceToken = await firebaseNotificationManager.getDeviceToken();
final response = await _apiService.login(
  token: deviceToken,
  version: await deviceInfo.appVersion,
  dateTime: DateTimeUtils.getCurrentDateTime(),
  device: deviceInfo.deviceTypeWithVersion,
  deviceType: kIsWeb ? 'android' : deviceInfo.deviceType,
  headers: _dataSource.getHeaders(withBaseAuth: true),
);
```
Refactored shape:
```dart
class AuthApiRepositoryImpl extends AuthRepository {
  AuthApiRepositoryImpl(
    this._apiService,
    this._dataSource, {
    required DeviceInfoManager deviceInfo,
    required FirebaseNotificationManager firebaseNotificationManager,
    required DateTimeProvider dateTimeProvider,
  })  : _deviceInfo = deviceInfo,
        _firebaseNotificationManager = firebaseNotificationManager,
        _dateTimeProvider = dateTimeProvider;
}
```
Unit test unlocked:
```dart
test('login sends device metadata and persists successful session', () async {
  when(() => fcm.getDeviceToken()).thenAnswer((_) async => 'push-token');
  when(() => device.appVersion).thenAnswer((_) async => '3.0.5');
  when(() => api.login(
    email: 'staff@test.com',
    password: 'secret',
    showLoader: false,
    token: 'push-token',
    version: '3.0.5',
    dateTime: any(named: 'dateTime'),
    device: any(named: 'device'),
    deviceType: any(named: 'deviceType'),
    headers: any(named: 'headers'),
  )).thenAnswer((_) async => successLoginResponse);

  final result = await repo.login(email: 'staff@test.com', password: 'secret', showLoader: false);

  expect(result.responseCode, HttpStatusCode.success);
  verify(() => storage.saveValue(LocalStorageKeys.isLoggedIn, true, SavedValueDataType.bool)).called(1);
});
```
### Example 2: Login page constructs its own BLoC and route dependency
Current code in `lib/presentation/screens/auth/login_page_view.dart`:
```dart
BlocProvider(
  create: (context) => AuthBloc(
    InjectionUtils.getUseCase<LoginUseCase>(),
  ),
  child: ...
)

InjectionUtils.getRouteManagement().goToWebViewScreen(
  AppConstants.ftStaffAppUrl,
  TranslationFile.howToLoginTitle,
);
```
Refactored shape:
```dart
class LoginPageView extends StatefulWidget {
  const LoginPageView({super.key, this.authBlocFactory, this.onHowToLogin});

  final AuthBloc Function()? authBlocFactory;
  final VoidCallback? onHowToLogin;
}
```
Widget test unlocked:
```dart
testWidgets('tapping how-to-login invokes injected callback', (tester) async {
  var tapped = false;
  await tester.pumpWidget(MaterialApp(
    home: LoginPageView(onHowToLogin: () => tapped = true),
  ));

  await tester.tap(find.text(TranslationFile.howToLogin));
  await tester.pump();

  expect(tapped, isTrue);
});
```
### Example 3: Location QR flow mixes parsing, storage lookup, toasts, navigation, time, and timers
Current code in `lib/presentation/blocs/location_check_in/location_check_in_bloc.dart`:
```dart
final loginClubId = kGetIt<DataSource>().getClubId();
if (scanData.clubId != loginClubId) {
  event.onClubMismatch?.call();
  return;
}

CheckInMemberDataModel _createMemberFromMqttEvent(UpdateCheckInFromMqttEvent event) =>
    CheckInMemberDataModel(
      member: event.memberName,
      memberId: event.memberId,
      checkinId: event.checkinId,
      datetime: event.dateTime ?? DateTime.now(),
    );

_checkedInMembersRefreshDebounce = Timer(const Duration(milliseconds: 800), () {
  add(GetCheckedInMembersEvent(locationId: locationId, showLoader: false));
});
```
Refactored shape:
```dart
LocationCheckInBloc({
  required DataSource dataSource,
  required ToastPort toast,
  required Clock clock,
  Duration checkedInRefreshDebounce = const Duration(milliseconds: 800),
  ...
}) : _dataSource = dataSource,
     _toast = toast,
     _clock = clock,
     _checkedInRefreshDebounce = checkedInRefreshDebounce;
```
Unit test unlocked:
```dart
blocTest<LocationCheckInBloc, LocationCheckInState>(
  'calls club mismatch callback when QR club differs from logged-in club',
  build: () {
    when(() => dataSource.getClubId()).thenReturn('club-1');
    return buildBlocWithLoadedLocation();
  },
  act: (bloc) => bloc.add(HandleQrCodeScanDataEvent(
    barcode: jsonEncode({'clubId': 'club-2', 'memberId': 'm1', 'memberNum': '100'}),
    context: fakeContext,
    onClubMismatch: () => mismatchCalled = true,
  )),
  verify: (_) => expect(mismatchCalled, isTrue),
);
```

<a id="12-flutter-test-setup"></a>

## 12. Recommended `flutter_test` Setup
- Current dev dependencies already include `flutter_test`, `bloc_test`, and `mocktail`. That is enough for most unit/widget tests.
- Add `build_runner` only if the team chooses generated mocks or generated fixtures later; with `mocktail`, it is not required today.
- Project-specific helpers to add: `test/helpers/pump_app.dart` for `MaterialApp`/`ScreenUtilInit` wrappers, `test/helpers/fakes.dart` for fake model builders, `test/helpers/mock_route_management.dart`, `test/helpers/fake_storage.dart`, and `test/helpers/network_response_builder.dart`.
- For local storage tests use `SharedPreferences.setMockInitialValues({})`. For GetIt-bound legacy widgets, call `await kGetIt.reset()` in `setUp`/`tearDown` until those widgets are refactored.
- Commands:
  - Unit/widget together: `flutter test`
  - One file: `flutter test test/presentation/blocs/auth/auth_bloc_test.dart`
  - Coverage: `flutter test --coverage`
  - Integration tests after adding `integration_test/`: `flutter test integration_test`
  - Flavor smoke build examples: `flutter run -t lib/main_dev.dart`, `flutter build web -t lib/main_prod.dart`
## Summary Table
| Module | Files Total | Testable Now | Needs Refactor | Skip | Estimated Effort |
|--------|-------------|--------------|----------------|------|-----------------|
| Auth | 17 | 9 | 3 | 5 | Small-Medium |
| App Bootstrap / Flavors | 5 | 3 | 1 | 1 | Small-Medium |
| Configuration | 2 | 0 | 1 | 1 | Small-Medium |
| Core | 7 | 3 | 1 | 3 | Small-Medium |
| Data Core | 6 | 4 | 0 | 2 | Small |
| Dependency Injection | 10 | 0 | 7 | 3 | Medium |
| Domain Core | 3 | 1 | 0 | 2 | Small |
| Domain Use Cases | 3 | 2 | 0 | 1 | Small |
| Event Check-In | 30 | 9 | 6 | 15 | Medium |
| Food Ordering | 52 | 16 | 18 | 18 | Large |
| Golf Bag Request | 30 | 10 | 5 | 15 | Medium |
| Home / Dashboard / FT Messaging Entry | 18 | 7 | 4 | 7 | Medium |
| Integrations | 1 | 1 | 0 | 0 | Small |
| Inventory Stock Count | 28 | 9 | 7 | 12 | Medium |
| Lesson Pro | 40 | 14 | 4 | 22 | Medium |
| Local Storage / Platform Services | 23 | 9 | 12 | 2 | Large |
| Location Check-In | 42 | 12 | 10 | 20 | Medium |
| MQTT | 3 | 2 | 1 | 0 | Small-Medium |
| Manual Check-In | 3 | 2 | 1 | 0 | Small-Medium |
| Member Photos | 22 | 7 | 5 | 10 | Medium |
| Member Search | 6 | 2 | 0 | 4 | Small |
| Photo Gallery / Create Album | 54 | 17 | 14 | 23 | Large |
| Presentation Shell | 10 | 2 | 2 | 6 | Small-Medium |
| Push / Web Notification | 55 | 22 | 13 | 20 | Large |
| Remote API / Network | 5 | 1 | 1 | 3 | Small-Medium |
| Repositories | 35 | 13 | 3 | 19 | Small-Medium |
| Resources / Theme | 13 | 0 | 0 | 13 | Small-Medium |
| Routing | 8 | 4 | 3 | 1 | Small-Medium |
| Shared Data Models | 66 | 65 | 0 | 1 | Small-Medium |
| Shared Mappers | 14 | 13 | 0 | 1 | Small-Medium |
| Shared Widgets | 34 | 2 | 14 | 18 | Large |
| Splash | 5 | 3 | 1 | 1 | Small-Medium |
| Starter & Timesheet | 13 | 7 | 1 | 5 | Small-Medium |
| Utilities | 22 | 13 | 6 | 3 | Medium |
| Valet | 28 | 8 | 5 | 15 | Medium |
