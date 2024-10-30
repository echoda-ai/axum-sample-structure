### Models (models/todo.rs and models/task.rs):
```rs
// models/todo.rs
use serde::{Deserialize, Serialize};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Todo {
    pub id: i32,
    pub title: String,
    pub description: Option<String>,
}

// models/task.rs
use serde::{Deserialize, Serialize};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Task {
    pub id: i32,
    pub todo_id: i32,  // Foreign key linking Task to a Todo
    pub title: String,
    pub is_completed: bool,
}

```
Handlers (handlers/todo_handler.rs and handlers/task_handler.rs):
```r
Copy code
// handlers/todo_handler.rs
use axum::{extract::Path, Json};
use crate::models::todo::Todo;
use crate::services::todo_service;
use std::sync::Arc;

pub async fn create_todo(
    Json(payload): Json<Todo>
) -> Json<Todo> {
    let todo = todo_service::create(payload).await;
    Json(todo)
}

pub async fn get_todo(
    Path(id): Path<i32>
) -> Option<Json<Todo>> {
    todo_service::get(id).await.map(Json)
}

pub async fn update_todo(
    Path(id): Path<i32>,
    Json(payload): Json<Todo>
) -> Option<Json<Todo>> {
    todo_service::update(id, payload).await.map(Json)
}

pub async fn delete_todo(
    Path(id): Path<i32>
) -> Json<String> {
    todo_service::delete(id).await;
    Json("Todo deleted".into())
}
```
```rs
// handlers/task_handler.rs
use axum::{extract::Path, Json};
use crate::models::task::Task;
use crate::services::task_service;
use std::sync::Arc;

pub async fn create_task(
    Json(payload): Json<Task>
) -> Json<Task> {
    let task = task_service::create(payload).await;
    Json(task)
}

pub async fn get_task(
    Path(id): Path<i32>
) -> Option<Json<Task>> {
    task_service::get(id).await.map(Json)
}

pub async fn update_task(
    Path(id): Path<i32>,
    Json(payload): Json<Task>
) -> Option<Json<Task>> {
    task_service::update(id, payload).await.map(Json)
}

pub async fn delete_task(
    Path(id): Path<i32>
) -> Json<String> {
    task_service::delete(id).await;
    Json("Task deleted".into())
}
```
Services (services/todo_service.rs and services/task_service.rs):
```rs
// services/todo_service.rs
use crate::models::todo::Todo;

pub async fn create(todo: Todo) -> Todo {
    // Logic to save `todo` in database
    todo
}

pub async fn get(id: i32) -> Option<Todo> {
    // Logic to retrieve `todo` by `id`
    None
}

pub async fn update(id: i32, todo: Todo) -> Option<Todo> {
    // Logic to update `todo`
    Some(todo)
}

pub async fn delete(id: i32) {
    // Logic to delete `todo`
}
```

```rs
// services/task_service.rs
use crate::models::task::Task;

pub async fn create(task: Task) -> Task {
    // Logic to save `task` in database
    task
}

pub async fn get(id: i32) -> Option<Task> {
    // Logic to retrieve `task` by `id`
    None
}

pub async fn update(id: i32, task: Task) -> Option<Task> {
    // Logic to update `task`
    Some(task)
}

pub async fn delete(id: i32) {
    // Logic to delete `task`
}
```
Routes (routes/todo_routes.rs and routes/task_routes.rs):
```rs
// routes/todo_routes.rs
use axum::{routing::get, Router};
use crate::handlers::todo_handler;

pub fn todo_routes() -> Router {
    Router::new()
        .route("/todos", get(todo_handler::create_todo))
        .route("/todos/:id", get(todo_handler::get_todo))
        .route("/todos/:id", get(todo_handler::update_todo))
        .route("/todos/:id", get(todo_handler::delete_todo))
}
```
```rs
// routes/task_routes.rs
use axum::{routing::get, Router};
use crate::handlers::task_handler;

pub fn task_routes() -> Router {
    Router::new()
        .route("/tasks", get(task_handler::create_task))
        .route("/tasks/:id", get(task_handler::get_task))
        .route("/tasks/:id", get(task_handler::update_task))
        .route("/tasks/:id", get(task_handler::delete_task))
}
```
Main Entry (main.rs):
```rs
use axum::Router;
use crate::routes::{todo_routes, task_routes};

#[tokio::main]
async fn main() {
    let app = Router::new()
        .merge(todo_routes())
        .merge(task_routes());

    axum::Server::bind(&"0.0.0.0:3000".parse().unwrap())
        .serve(app.into_make_service())
        .await
        .unwrap();
}
```
