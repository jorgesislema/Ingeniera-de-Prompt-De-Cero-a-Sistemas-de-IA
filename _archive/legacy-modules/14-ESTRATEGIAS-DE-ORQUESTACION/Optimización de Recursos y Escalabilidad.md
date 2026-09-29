# Optimización de Recursos y Escalabilidad

## Introducción: Eficiencia como Ventaja Competitiva

En el ecosistema de prompt engineering empresarial, la optimización de recursos no es solo una consideración técnica; es un multiplicador estratégico que determina la viabilidad económica y la escalabilidad de las soluciones de IA. Esta sección explora técnicas avanzadas para maximizar eficiencia mientras se mantiene calidad y rendimiento.

## Estrategias de Optimización de Costos

### 1. Sistema de Enrutamiento Inteligente de Modelos

**Router Dinámico de Modelos:**
```python
from typing import Dict, List, Optional, Tuple
from dataclasses import dataclass
from enum import Enum
import numpy as np
import asyncio
from datetime import datetime, timedelta

class ModelTier(Enum):
    ECONOMY = "economy"          # Modelos básicos, bajo costo
    STANDARD = "standard"        # Modelos balanceados
    PREMIUM = "premium"          # Modelos de alta capacidad
    SPECIALIZED = "specialized"  # Modelos especializados

@dataclass
class ModelConfig:
    model_id: str
    provider: str
    tier: ModelTier
    cost_per_token: float
    latency_avg_ms: float
    quality_score: float
    max_tokens: int
    capabilities: List[str]
    availability_sla: float

@dataclass
class RoutingDecision:
    selected_model: str
    confidence: float
    cost_estimate: float
    latency_estimate: float
    quality_estimate: float
    routing_reason: str

class IntelligentModelRouter:
    def __init__(self):
        self.model_registry = ModelRegistry()
        self.cost_optimizer = CostOptimizer()
        self.quality_predictor = QualityPredictor()
        self.latency_predictor = LatencyPredictor()
        self.routing_history = RoutingHistory()
        
    async def route_request(self, prompt: str, context: Dict,
                          routing_config: Dict) -> RoutingDecision:
        """Enruta request al modelo óptimo basado en múltiples criterios"""
        
        # Analizar características del prompt
        prompt_analysis = await self._analyze_prompt_characteristics(prompt, context)
        
        # Obtener modelos candidatos
        candidate_models = await self._get_candidate_models(
            prompt_analysis, routing_config
        )
        
        # Evaluar cada modelo candidato
        model_evaluations = []
        for model in candidate_models:
            evaluation = await self._evaluate_model_for_request(
                model, prompt_analysis, routing_config
            )
            model_evaluations.append(evaluation)
        
        # Seleccionar modelo óptimo
        optimal_model = self._select_optimal_model(
            model_evaluations, routing_config
        )
        
        # Registrar decisión de routing
        await self.routing_history.record_routing_decision(
            prompt_analysis, optimal_model, model_evaluations
        )
        
        return optimal_model
    
    async def _analyze_prompt_characteristics(self, prompt: str, 
                                            context: Dict) -> Dict:
        """Analiza características del prompt para optimizar routing"""
        
        characteristics = {
            "length": len(prompt),
            "complexity_score": 0.0,
            "domain": "general",
            "task_type": "generation",
            "quality_requirements": "standard",
            "latency_sensitivity": "medium",
            "cost_sensitivity": "medium"
        }
        
        # Análisis de complejidad
        complexity_indicators = [
            "analyze", "complex", "detailed", "comprehensive",
            "step-by-step", "reasoning", "logic", "compare"
        ]
        
        complexity_score = sum(
            1 for indicator in complexity_indicators 
            if indicator.lower() in prompt.lower()
        ) / len(complexity_indicators)
        
        characteristics["complexity_score"] = complexity_score
        
        # Detección de dominio
        domain_keywords = {
            "code": ["code", "programming", "function", "algorithm", "debug"],
            "legal": ["legal", "contract", "law", "regulation", "compliance"],
            "medical": ["medical", "diagnosis", "treatment", "symptoms", "health"],
            "financial": ["financial", "investment", "trading", "analysis", "market"],
            "creative": ["creative", "story", "poem", "writing", "artistic"]
        }
        
        for domain, keywords in domain_keywords.items():
            if any(keyword.lower() in prompt.lower() for keyword in keywords):
                characteristics["domain"] = domain
                break
        
        # Extraer requerimientos del contexto
        if context:
            characteristics.update({
                "quality_requirements": context.get("quality_level", "standard"),
                "latency_sensitivity": context.get("latency_requirement", "medium"),
                "cost_sensitivity": context.get("cost_requirement", "medium"),
                "user_tier": context.get("user_tier", "standard")
            })
        
        return characteristics
    
    async def _evaluate_model_for_request(self, model: ModelConfig,
                                        prompt_analysis: Dict,
                                        routing_config: Dict) -> Dict:
        """Evalúa un modelo específico para un request"""
        
        # Predicción de calidad
        quality_prediction = await self.quality_predictor.predict_quality(
            model, prompt_analysis
        )
        
        # Predicción de latencia
        latency_prediction = await self.latency_predictor.predict_latency(
            model, prompt_analysis
        )
        
        # Estimación de costo
        cost_prediction = await self.cost_optimizer.estimate_cost(
            model, prompt_analysis
        )
        
        # Verificar disponibilidad
        availability = await self._check_model_availability(model)
        
        # Calcular score compuesto
        composite_score = self._calculate_composite_score(
            quality_prediction, latency_prediction, cost_prediction,
            availability, prompt_analysis, routing_config
        )
        
        return {
            "model": model,
            "quality_prediction": quality_prediction,
            "latency_prediction": latency_prediction,
            "cost_prediction": cost_prediction,
            "availability": availability,
            "composite_score": composite_score
        }
    
    def _calculate_composite_score(self, quality_pred: float, latency_pred: float,
                                 cost_pred: float, availability: float,
                                 prompt_analysis: Dict, routing_config: Dict) -> float:
        """Calcula score compuesto para selección de modelo"""
        
        # Pesos basados en requerimientos
        weights = self._determine_weights(prompt_analysis, routing_config)
        
        # Normalizar métricas (0-1, donde 1 es mejor)
        quality_score = quality_pred  # Ya normalizada
        latency_score = 1.0 / (1.0 + latency_pred / 1000.0)  # Invertir latencia
        cost_score = 1.0 / (1.0 + cost_pred)  # Invertir costo
        availability_score = availability
        
        # Calcular score ponderado
        composite_score = (
            quality_score * weights["quality"] +
            latency_score * weights["latency"] +
            cost_score * weights["cost"] +
            availability_score * weights["availability"]
        )
        
        return composite_score
    
    def _determine_weights(self, prompt_analysis: Dict, 
                          routing_config: Dict) -> Dict[str, float]:
        """Determina pesos para criterios de selección"""
        
        # Pesos base
        base_weights = {
            "quality": 0.4,
            "latency": 0.3,
            "cost": 0.2,
            "availability": 0.1
        }
        
        # Ajustar basado en sensibilidades
        quality_sensitivity = prompt_analysis.get("quality_requirements", "standard")
        latency_sensitivity = prompt_analysis.get("latency_sensitivity", "medium")
        cost_sensitivity = prompt_analysis.get("cost_sensitivity", "medium")
        
        # Ajustes dinámicos
        if quality_sensitivity == "high":
            base_weights["quality"] += 0.2
            base_weights["cost"] -= 0.1
            base_weights["latency"] -= 0.1
        elif quality_sensitivity == "low":
            base_weights["quality"] -= 0.1
            base_weights["cost"] += 0.1
        
        if latency_sensitivity == "high":
            base_weights["latency"] += 0.2
            base_weights["cost"] -= 0.1
            base_weights["quality"] -= 0.1
        elif latency_sensitivity == "low":
            base_weights["latency"] -= 0.1
            base_weights["cost"] += 0.1
        
        if cost_sensitivity == "high":
            base_weights["cost"] += 0.2
            base_weights["quality"] -= 0.1
            base_weights["latency"] -= 0.1
        
        # Normalizar pesos
        total_weight = sum(base_weights.values())
        normalized_weights = {k: v / total_weight for k, v in base_weights.items()}
        
        return normalized_weights
```

### 2. Sistema de Cache Inteligente Multi-Nivel

**Cache Jerárquico Avanzado:**
```python
class IntelligentCacheSystem:
    def __init__(self):
        self.cache_levels = {
            "l1_memory": MemoryCache(size_mb=256, ttl_seconds=300),
            "l2_redis": RedisCache(size_mb=2048, ttl_seconds=3600),
            "l3_persistent": PersistentCache(size_gb=10, ttl_hours=24)
        }
        self.semantic_index = SemanticIndex()
        self.cache_optimizer = CacheOptimizer()
        self.eviction_manager = EvictionManager()
        
    async def get_cached_response(self, prompt: str, context: Dict) -> Optional[Dict]:
        """Busca respuesta en cache con matching semántico inteligente"""
        
        # Generar clave de cache semántica
        cache_key = await self._generate_semantic_cache_key(prompt, context)
        
        # Buscar en niveles de cache en orden
        for level_name, cache_level in self.cache_levels.items():
            # Búsqueda exacta
            exact_match = await cache_level.get(cache_key)
            if exact_match:
                await self._record_cache_hit(level_name, "exact", cache_key)
                return exact_match
            
            # Búsqueda semántica (solo en niveles superiores)
            if level_name in ["l2_redis", "l3_persistent"]:
                semantic_matches = await self._find_semantic_matches(
                    prompt, context, cache_level
                )
                
                if semantic_matches:
                    best_match = self._select_best_semantic_match(
                        semantic_matches, prompt, context
                    )
                    
                    if best_match["similarity_score"] > 0.85:  # Threshold
                        await self._record_cache_hit(level_name, "semantic", cache_key)
                        
                        # Promover a niveles superiores
                        await self._promote_cache_entry(best_match, cache_key)
                        
                        return best_match["response"]
        
        await self._record_cache_miss(cache_key)
        return None
    
    async def store_response(self, prompt: str, context: Dict, 
                           response: Dict, metadata: Dict):
        """Almacena respuesta en niveles apropiados de cache"""
        
        cache_key = await self._generate_semantic_cache_key(prompt, context)
        
        # Analizar valor de la respuesta para determinar niveles de storage
        storage_analysis = await self._analyze_storage_value(
            prompt, context, response, metadata
        )
        
        # Almacenar en niveles apropiados
        storage_tasks = []
        
        for level_name, should_store in storage_analysis.items():
            if should_store:
                cache_level = self.cache_levels[level_name]
                
                # Preparar data para storage
                cache_entry = self._prepare_cache_entry(
                    prompt, context, response, metadata, level_name
                )
                
                # Almacenar de forma asíncrona
                storage_tasks.append(
                    cache_level.set(cache_key, cache_entry, 
                                  ttl=storage_analysis[f"{level_name}_ttl"])
                )
        
        # Ejecutar storage en paralelo
        await asyncio.gather(*storage_tasks)
        
        # Actualizar índice semántico
        await self.semantic_index.add_entry(cache_key, prompt, context, response)
        
        # Trigger optimización de cache si es necesario
        await self._trigger_cache_optimization_if_needed()
    
    async def _find_semantic_matches(self, prompt: str, context: Dict,
                                   cache_level) -> List[Dict]:
        """Encuentra matches semánticos en un nivel de cache"""
        
        # Generar embedding del prompt
        prompt_embedding = await self.semantic_index.generate_embedding(prompt)
        
        # Buscar entradas similares
        similar_entries = await self.semantic_index.find_similar(
            prompt_embedding, threshold=0.7, limit=10
        )
        
        # Verificar disponibilidad en cache level
        valid_matches = []
        for entry in similar_entries:
            cache_entry = await cache_level.get(entry["cache_key"])
            if cache_entry:
                valid_matches.append({
                    "cache_key": entry["cache_key"],
                    "similarity_score": entry["similarity_score"],
                    "response": cache_entry,
                    "original_prompt": entry["prompt"],
                    "original_context": entry["context"]
                })
        
        return valid_matches
    
    async def _analyze_storage_value(self, prompt: str, context: Dict,
                                   response: Dict, metadata: Dict) -> Dict:
        """Analiza valor de una respuesta para determinar estrategia de cache"""
        
        analysis_factors = {
            "generation_cost": metadata.get("cost", 0.0),
            "generation_time": metadata.get("duration_ms", 0),
            "quality_score": metadata.get("quality_score", 0.5),
            "prompt_complexity": await self._assess_prompt_complexity(prompt),
            "reuse_probability": await self._estimate_reuse_probability(prompt, context),
            "data_sensitivity": self._assess_data_sensitivity(prompt, context, response)
        }
        
        storage_decision = {
            "l1_memory": False,
            "l2_redis": False,
            "l3_persistent": False,
            "l1_memory_ttl": 300,
            "l2_redis_ttl": 3600,
            "l3_persistent_ttl": 86400
        }
        
        # Decisiones basadas en factores
        
        # L1 Memory - Para respuestas frecuentes y rápidas
        if (analysis_factors["reuse_probability"] > 0.7 or
            analysis_factors["generation_time"] > 2000):  # >2s
            storage_decision["l1_memory"] = True
        
        # L2 Redis - Para respuestas valiosas de mediano plazo
        if (analysis_factors["generation_cost"] > 0.01 or
            analysis_factors["quality_score"] > 0.7 or
            analysis_factors["prompt_complexity"] > 0.6):
            storage_decision["l2_redis"] = True
        
        # L3 Persistent - Para respuestas muy valiosas de largo plazo
        if (analysis_factors["generation_cost"] > 0.05 or
            analysis_factors["quality_score"] > 0.8 or
            (analysis_factors["prompt_complexity"] > 0.8 and 
             analysis_factors["reuse_probability"] > 0.3)):
            storage_decision["l3_persistent"] = True
        
        # Ajustar TTL basado en sensibilidad de datos
        if analysis_factors["data_sensitivity"] == "high":
            storage_decision["l1_memory_ttl"] = 60
            storage_decision["l2_redis_ttl"] = 300
            storage_decision["l3_persistent_ttl"] = 3600
        
        return storage_decision
```

### 3. Sistema de Batch Processing Optimizado

**Procesamiento en Lotes Inteligente:**
```python
class OptimizedBatchProcessor:
    def __init__(self):
        self.batch_optimizer = BatchOptimizer()
        self.load_balancer = LoadBalancer()
        self.cost_analyzer = CostAnalyzer()
        self.priority_queue = PriorityQueue()
        
    async def process_batch_requests(self, requests: List[Dict],
                                   processing_config: Dict) -> Dict[str, Any]:
        """Procesa lote de requests con optimización de costos y recursos"""
        
        batch_id = str(uuid.uuid4())
        
        # Analizar y agrupar requests
        request_analysis = await self._analyze_batch_requests(requests)
        
        # Optimizar agrupación de batches
        optimized_batches = await self.batch_optimizer.optimize_batching(
            requests, request_analysis, processing_config
        )
        
        # Programar ejecución de batches
        execution_schedule = await self._schedule_batch_execution(
            optimized_batches, processing_config
        )
        
        # Ejecutar batches según schedule
        batch_results = await self._execute_scheduled_batches(
            execution_schedule, batch_id
        )
        
        # Analizar resultados y costos
        cost_analysis = await self.cost_analyzer.analyze_batch_costs(
            batch_results, execution_schedule
        )
        
        return {
            "batch_id": batch_id,
            "total_requests": len(requests),
            "batches_created": len(optimized_batches),
            "execution_schedule": execution_schedule,
            "results": batch_results,
            "cost_analysis": cost_analysis,
            "optimization_metrics": self._calculate_optimization_metrics(
                requests, optimized_batches, cost_analysis
            )
        }
    
    async def _analyze_batch_requests(self, requests: List[Dict]) -> Dict:
        """Analiza requests para optimización de batching"""
        
        analysis = {
            "request_types": {},
            "model_requirements": {},
            "priority_distribution": {},
            "cost_estimates": {},
            "batching_opportunities": []
        }
        
        # Clasificar requests por tipo
        type_counts = {}
        for request in requests:
            req_type = self._classify_request_type(request)
            type_counts[req_type] = type_counts.get(req_type, 0) + 1
        
        analysis["request_types"] = type_counts
        
        # Analizar requerimientos de modelo
        model_requirements = {}
        for request in requests:
            required_model = self._determine_required_model(request)
            if required_model not in model_requirements:
                model_requirements[required_model] = []
            model_requirements[required_model].append(request["id"])
        
        analysis["model_requirements"] = model_requirements
        
        # Analizar distribución de prioridades
        priorities = [request.get("priority", 0) for request in requests]
        analysis["priority_distribution"] = {
            "high": len([p for p in priorities if p > 7]),
            "medium": len([p for p in priorities if 3 <= p <= 7]),
            "low": len([p for p in priorities if p < 3])
        }
        
        # Identificar oportunidades de batching
        batching_opportunities = await self._identify_batching_opportunities(
            requests, model_requirements
        )
        analysis["batching_opportunities"] = batching_opportunities
        
        return analysis
    
    async def _optimize_batch_grouping(self, requests: List[Dict],
                                     analysis: Dict,
                                     config: Dict) -> List[Dict]:
        """Optimiza agrupación de requests en batches"""
        
        # Parámetros de optimización
        max_batch_size = config.get("max_batch_size", 50)
        priority_grouping = config.get("priority_grouping", True)
        model_grouping = config.get("model_grouping", True)
        cost_optimization = config.get("cost_optimization", True)
        
        optimized_batches = []
        remaining_requests = requests.copy()
        
        # Agrupar por prioridad si está habilitado
        if priority_grouping:
            priority_groups = self._group_by_priority(remaining_requests)
            
            for priority, group_requests in priority_groups.items():
                # Procesar grupo de prioridad
                priority_batches = await self._create_batches_for_priority_group(
                    group_requests, analysis, config
                )
                optimized_batches.extend(priority_batches)
        else:
            # Procesamiento sin agrupación por prioridad
            general_batches = await self._create_optimized_batches(
                remaining_requests, analysis, config
            )
            optimized_batches.extend(general_batches)
        
        return optimized_batches
    
    async def _create_batches_for_priority_group(self, requests: List[Dict],
                                               analysis: Dict,
                                               config: Dict) -> List[Dict]:
        """Crea batches optimizados para un grupo de prioridad"""
        
        batches = []
        
        # Agrupar por modelo si está habilitado
        if config.get("model_grouping", True):
            model_groups = {}
            for request in requests:
                model = self._determine_required_model(request)
                if model not in model_groups:
                    model_groups[model] = []
                model_groups[model].append(request)
            
            # Crear batches para cada grupo de modelo
            for model, model_requests in model_groups.items():
                model_batches = await self._create_model_specific_batches(
                    model_requests, model, config
                )
                batches.extend(model_batches)
        else:
            # Crear batches sin agrupación por modelo
            general_batches = await self._create_size_optimized_batches(
                requests, config
            )
            batches.extend(general_batches)
        
        return batches
    
    async def _schedule_batch_execution(self, batches: List[Dict],
                                      config: Dict) -> Dict:
        """Programa ejecución óptima de batches"""
        
        # Analizar capacidad disponible
        available_capacity = await self._assess_available_capacity()
        
        # Estimar costos por tiempo de ejecución
        time_cost_analysis = await self._analyze_time_based_costs()
        
        # Crear schedule optimizado
        schedule = {
            "immediate": [],
            "peak_hours": [],
            "off_peak": [],
            "background": []
        }
        
        for batch in batches:
            # Determinar categoría de programación
            scheduling_category = self._determine_scheduling_category(
                batch, available_capacity, time_cost_analysis, config
            )
            
            schedule[scheduling_category].append({
                "batch": batch,
                "estimated_cost": batch["cost_estimate"],
                "estimated_duration": batch["duration_estimate"],
                "resource_requirements": batch["resource_requirements"]
            })
        
        # Optimizar orden dentro de cada categoría
        for category in schedule:
            schedule[category] = self._optimize_execution_order(
                schedule[category], category, config
            )
        
        return schedule
    
    async def _execute_scheduled_batches(self, schedule: Dict,
                                       batch_id: str) -> Dict[str, Any]:
        """Ejecuta batches según programación optimizada"""
        
        execution_results = {
            "immediate": [],
            "peak_hours": [],
            "off_peak": [],
            "background": [],
            "total_cost": 0.0,
            "total_duration": 0.0,
            "success_rate": 0.0
        }
        
        # Ejecutar batches inmediatos
        if schedule["immediate"]:
            immediate_results = await self._execute_immediate_batches(
                schedule["immediate"], batch_id
            )
            execution_results["immediate"] = immediate_results
        
        # Programar batches para horas pico (si aplica)
        if schedule["peak_hours"]:
            peak_schedule = await self._schedule_peak_hour_execution(
                schedule["peak_hours"], batch_id
            )
            execution_results["peak_hours"] = peak_schedule
        
        # Programar batches para horas valle
        if schedule["off_peak"]:
            off_peak_schedule = await self._schedule_off_peak_execution(
                schedule["off_peak"], batch_id
            )
            execution_results["off_peak"] = off_peak_schedule
        
        # Programar batches de background
        if schedule["background"]:
            background_schedule = await self._schedule_background_execution(
                schedule["background"], batch_id
            )
            execution_results["background"] = background_schedule
        
        # Calcular métricas agregadas
        execution_results["total_cost"] = sum(
            sum(result.get("cost", 0) for result in category_results)
            for category_results in execution_results.values()
            if isinstance(category_results, list)
        )
        
        return execution_results
```

## Arquitecturas de Escalabilidad Horizontal

### 1. Sistema de Auto-Scaling Inteligente

**Auto-Scaling Predictivo:**
```python
class PredictiveAutoScaler:
    def __init__(self):
        self.load_predictor = LoadPredictor()
        self.capacity_planner = CapacityPlanner()
        self.scaling_executor = ScalingExecutor()
        self.cost_optimizer = CostOptimizer()
        
    async def monitor_and_scale(self, monitoring_config: Dict):
        """Monitorea carga y ejecuta scaling predictivo"""
        
        while True:
            try:
                # Recopilar métricas actuales
                current_metrics = await self._collect_current_metrics()
                
                # Predecir carga futura
                load_prediction = await self.load_predictor.predict_load(
                    current_metrics, monitoring_config
                )
                
                # Analizar necesidades de scaling
                scaling_analysis = await self._analyze_scaling_needs(
                    current_metrics, load_prediction, monitoring_config
                )
                
                # Ejecutar scaling si es necesario
                if scaling_analysis["scaling_needed"]:
                    scaling_result = await self._execute_scaling_action(
                        scaling_analysis, monitoring_config
                    )
                    
                    await self._log_scaling_action(scaling_result)
                
                # Esperar antes del siguiente ciclo
                await asyncio.sleep(monitoring_config.get("check_interval", 60))
                
            except Exception as e:
                await self._handle_monitoring_error(e)
                await asyncio.sleep(30)  # Retry más rápido en caso de error
    
    async def _analyze_scaling_needs(self, current_metrics: Dict,
                                   load_prediction: Dict,
                                   config: Dict) -> Dict:
        """Analiza si se necesita scaling basado en métricas y predicciones"""
        
        analysis = {
            "scaling_needed": False,
            "scaling_direction": None,  # "up" or "down"
            "scaling_magnitude": 0,
            "scaling_urgency": "normal",  # "low", "normal", "high", "critical"
            "cost_impact": 0.0,
            "reasons": []
        }
        
        # Analizar métricas actuales
        cpu_utilization = current_metrics.get("cpu_utilization", 0)
        memory_utilization = current_metrics.get("memory_utilization", 0)
        request_rate = current_metrics.get("requests_per_second", 0)
        response_time = current_metrics.get("avg_response_time", 0)
        queue_length = current_metrics.get("queue_length", 0)
        
        # Thresholds de scaling
        scale_up_thresholds = config.get("scale_up_thresholds", {
            "cpu_utilization": 70,
            "memory_utilization": 80,
            "response_time": 5000,  # ms
            "queue_length": 100
        })
        
        scale_down_thresholds = config.get("scale_down_thresholds", {
            "cpu_utilization": 30,
            "memory_utilization": 40,
            "response_time": 1000,  # ms
            "queue_length": 10
        })
        
        # Evaluar necesidad de scale up
        scale_up_indicators = []
        
        if cpu_utilization > scale_up_thresholds["cpu_utilization"]:
            scale_up_indicators.append(f"High CPU utilization: {cpu_utilization}%")
        
        if memory_utilization > scale_up_thresholds["memory_utilization"]:
            scale_up_indicators.append(f"High memory utilization: {memory_utilization}%")
        
        if response_time > scale_up_thresholds["response_time"]:
            scale_up_indicators.append(f"High response time: {response_time}ms")
        
        if queue_length > scale_up_thresholds["queue_length"]:
            scale_up_indicators.append(f"High queue length: {queue_length}")
        
        # Evaluar predicciones futuras
        predicted_load_increase = load_prediction.get("load_increase_factor", 1.0)
        if predicted_load_increase > 1.5:  # 50% increase predicted
            scale_up_indicators.append(f"Predicted load increase: {predicted_load_increase:.1f}x")
        
        # Evaluar necesidad de scale down
        scale_down_indicators = []
        
        if (cpu_utilization < scale_down_thresholds["cpu_utilization"] and
            memory_utilization < scale_down_thresholds["memory_utilization"] and
            response_time < scale_down_thresholds["response_time"] and
            queue_length < scale_down_thresholds["queue_length"]):
            
            # Verificar que la condición sea estable
            if self._is_low_utilization_stable(current_metrics, config):
                scale_down_indicators.append("Consistently low resource utilization")
        
        # Determinar acción de scaling
        if scale_up_indicators:
            analysis["scaling_needed"] = True
            analysis["scaling_direction"] = "up"
            analysis["reasons"] = scale_up_indicators
            
            # Determinar magnitud basada en severidad
            severity_score = len(scale_up_indicators) / 4.0  # Normalizar
            analysis["scaling_magnitude"] = max(1, int(severity_score * 3))
            
            # Determinar urgencia
            if queue_length > scale_up_thresholds["queue_length"] * 2:
                analysis["scaling_urgency"] = "critical"
            elif cpu_utilization > 90 or memory_utilization > 95:
                analysis["scaling_urgency"] = "high"
            else:
                analysis["scaling_urgency"] = "normal"
                
        elif scale_down_indicators and not load_prediction.get("load_increase_expected", False):
            analysis["scaling_needed"] = True
            analysis["scaling_direction"] = "down"
            analysis["reasons"] = scale_down_indicators
            analysis["scaling_magnitude"] = 1
            analysis["scaling_urgency"] = "low"
        
        # Calcular impacto de costo
        if analysis["scaling_needed"]:
            analysis["cost_impact"] = await self._calculate_scaling_cost_impact(
                analysis, current_metrics
            )
        
        return analysis
    
    async def _execute_scaling_action(self, scaling_analysis: Dict,
                                    config: Dict) -> Dict:
        """Ejecuta acción de scaling basada en análisis"""
        
        scaling_result = {
            "action_id": str(uuid.uuid4()),
            "timestamp": datetime.utcnow(),
            "direction": scaling_analysis["scaling_direction"],
            "magnitude": scaling_analysis["scaling_magnitude"],
            "success": False,
            "new_capacity": 0,
            "cost_impact": scaling_analysis["cost_impact"],
            "execution_time": 0.0
        }
        
        start_time = time.time()
        
        try:
            if scaling_analysis["scaling_direction"] == "up":
                result = await self.scaling_executor.scale_up(
                    magnitude=scaling_analysis["scaling_magnitude"],
                    urgency=scaling_analysis["scaling_urgency"],
                    config=config
                )
            else:
                result = await self.scaling_executor.scale_down(
                    magnitude=scaling_analysis["scaling_magnitude"],
                    config=config
                )
            
            scaling_result["success"] = result["success"]
            scaling_result["new_capacity"] = result["new_capacity"]
            scaling_result["details"] = result
            
        except Exception as e:
            scaling_result["error"] = str(e)
            await self._handle_scaling_error(e, scaling_analysis)
        
        finally:
            scaling_result["execution_time"] = time.time() - start_time
        
        return scaling_result
```

### 2. Load Balancing Inteligente

**Balanceador de Carga Adaptativo:**
```python
class IntelligentLoadBalancer:
    def __init__(self):
        self.node_monitor = NodeMonitor()
        self.routing_algorithms = {
            "round_robin": RoundRobinRouter(),
            "weighted_round_robin": WeightedRoundRobinRouter(),
            "least_connections": LeastConnectionsRouter(),
            "least_response_time": LeastResponseTimeRouter(),
            "resource_based": ResourceBasedRouter(),
            "predictive": PredictiveRouter()
        }
        self.performance_tracker = PerformanceTracker()
        self.adaptation_engine = AdaptationEngine()
        
    async def route_request(self, request: Dict, routing_config: Dict) -> str:
        """Enruta request al nodo óptimo usando algoritmo adaptativo"""
        
        # Obtener estado actual de nodos
        node_states = await self.node_monitor.get_node_states()
        
        # Filtrar nodos disponibles
        available_nodes = [
            node for node in node_states 
            if node["status"] == "healthy" and node["accepting_requests"]
        ]
        
        if not available_nodes:
            raise Exception("No healthy nodes available")
        
        # Seleccionar algoritmo de routing
        routing_algorithm = self._select_routing_algorithm(
            request, available_nodes, routing_config
        )
        
        # Ejecutar routing
        selected_node = await routing_algorithm.route(
            request, available_nodes, routing_config
        )
        
        # Registrar decisión de routing
        await self.performance_tracker.record_routing_decision(
            request["id"], selected_node, routing_algorithm.name
        )
        
        return selected_node
    
    def _select_routing_algorithm(self, request: Dict, available_nodes: List[Dict],
                                config: Dict) -> 'RoutingAlgorithm':
        """Selecciona algoritmo de routing óptimo para el request"""
        
        # Analizar características del request
        request_analysis = {
            "complexity": self._analyze_request_complexity(request),
            "priority": request.get("priority", 0),
            "expected_duration": self._estimate_request_duration(request),
            "resource_requirements": self._estimate_resource_requirements(request)
        }
        
        # Analizar estado de los nodos
        node_analysis = {
            "load_variance": self._calculate_load_variance(available_nodes),
            "performance_variance": self._calculate_performance_variance(available_nodes),
            "capacity_variance": self._calculate_capacity_variance(available_nodes)
        }
        
        # Selección de algoritmo basada en condiciones
        
        # Para requests simples con nodos homogéneos - Round Robin
        if (request_analysis["complexity"] < 0.3 and 
            node_analysis["load_variance"] < 0.2):
            return self.routing_algorithms["round_robin"]
        
        # Para requests con alta prioridad - Least Response Time
        if request_analysis["priority"] > 7:
            return self.routing_algorithms["least_response_time"]
        
        # Para nodos con capacidades heterogéneas - Resource Based
        if node_analysis["capacity_variance"] > 0.4:
            return self.routing_algorithms["resource_based"]
        
        # Para patrones de carga predecibles - Predictive
        if self._has_predictable_patterns(available_nodes):
            return self.routing_algorithms["predictive"]
        
        # Default: Weighted Round Robin
        return self.routing_algorithms["weighted_round_robin"]
    
    async def adapt_routing_strategy(self, performance_window: timedelta = timedelta(minutes=15)):
        """Adapta estrategia de routing basada en performance histórica"""
        
        # Analizar performance reciente
        recent_performance = await self.performance_tracker.get_performance_window(
            performance_window
        )
        
        # Evaluar efectividad de algoritmos
        algorithm_effectiveness = {}
        for algorithm_name in self.routing_algorithms.keys():
            effectiveness = self._evaluate_algorithm_effectiveness(
                algorithm_name, recent_performance
            )
            algorithm_effectiveness[algorithm_name] = effectiveness
        
        # Identificar patrones de performance
        performance_patterns = self._identify_performance_patterns(recent_performance)
        
        # Generar recomendaciones de adaptación
        adaptation_recommendations = self.adaptation_engine.generate_recommendations(
            algorithm_effectiveness, performance_patterns
        )
        
        # Aplicar adaptaciones si es necesario
        for recommendation in adaptation_recommendations:
            if recommendation["confidence"] > 0.8:
                await self._apply_routing_adaptation(recommendation)
                
                await self.performance_tracker.log_adaptation(
                    recommendation, datetime.utcnow()
                )
```

Esta sección proporciona las herramientas y estrategias fundamentales para construir sistemas de prompt engineering que sean no solo técnicamente sólidos, sino también económicamente viables y escalables a nivel empresarial.
