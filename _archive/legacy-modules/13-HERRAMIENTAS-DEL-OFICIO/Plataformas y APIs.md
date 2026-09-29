# Plataformas y APIs para Prompt Engineering

## Introducción: El Ecosistema de Herramientas Modernas

El prompt engineering profesional requiere un arsenal de herramientas especializadas que permitan desarrollo, testing, deployment y monitoreo de prompts a escala empresarial. Esta sección explora las plataformas más avanzadas y cómo maximizar su potencial.

## Principales Proveedores de LLM APIs

### 1. OpenAI Platform

**Características Empresariales:**
```python
import openai
from typing import Dict, List, Optional
import asyncio
import time

class OpenAIPromptManager:
    def __init__(self, api_key: str, organization: Optional[str] = None):
        self.client = openai.OpenAI(
            api_key=api_key,
            organization=organization
        )
        self.model_configs = {
            "gpt-4o": {
                "max_tokens": 4096,
                "temperature": 0.1,
                "top_p": 0.9,
                "multimodal": True
            },
            "gpt-4o-mini": {
                "max_tokens": 4096,
                "temperature": 0.1,
                "cost_effective": True
            }
        }
    
    async def generate_with_retry(self, messages: List[Dict], model: str = "gpt-4o", 
                                 max_retries: int = 3) -> Dict:
        """Genera respuesta con retry automático y manejo de errores"""
        
        config = self.model_configs.get(model, self.model_configs["gpt-4o"])
        
        for attempt in range(max_retries):
            try:
                response = await self.client.chat.completions.create(
                    model=model,
                    messages=messages,
                    **config
                )
                
                return {
                    "content": response.choices[0].message.content,
                    "usage": response.usage.model_dump(),
                    "model": model,
                    "attempt": attempt + 1,
                    "timestamp": time.time()
                }
                
            except openai.RateLimitError as e:
                wait_time = 2 ** attempt
                await asyncio.sleep(wait_time)
                continue
                
            except openai.APIError as e:
                if attempt == max_retries - 1:
                    raise Exception(f"API failed after {max_retries} attempts: {e}")
                await asyncio.sleep(1)
                continue
        
        raise Exception(f"Failed to generate response after {max_retries} attempts")
    
    def batch_process_prompts(self, prompt_batch: List[Dict], 
                             concurrency_limit: int = 10) -> List[Dict]:
        """Procesa lote de prompts con control de concurrencia"""
        
        async def process_batch():
            semaphore = asyncio.Semaphore(concurrency_limit)
            
            async def process_single_prompt(prompt_data):
                async with semaphore:
                    return await self.generate_with_retry(
                        prompt_data["messages"],
                        prompt_data.get("model", "gpt-4o")
                    )
            
            tasks = [process_single_prompt(prompt) for prompt in prompt_batch]
            return await asyncio.gather(*tasks)
        
        return asyncio.run(process_batch())
    
    def estimate_costs(self, messages: List[Dict], model: str = "gpt-4o") -> Dict:
        """Estima costos antes de ejecutar"""
        
        pricing = {
            "gpt-4o": {"input": 0.005, "output": 0.015},
            "gpt-4o-mini": {"input": 0.0005, "output": 0.0015}
        }
        
        # Estimación simplificada de tokens
        input_tokens = sum(len(msg["content"].split()) * 1.3 for msg in messages)
        estimated_output_tokens = 500  # Estimación conservadora
        
        model_pricing = pricing.get(model, pricing["gpt-4o"])
        
        input_cost = (input_tokens / 1000) * model_pricing["input"]
        output_cost = (estimated_output_tokens / 1000) * model_pricing["output"]
        
        return {
            "estimated_input_tokens": int(input_tokens),
            "estimated_output_tokens": estimated_output_tokens,
            "estimated_input_cost": input_cost,
            "estimated_output_cost": output_cost,
            "total_estimated_cost": input_cost + output_cost,
            "model": model
        }
```

**Optimización Avanzada de OpenAI:**
```python
class OpenAIOptimizer:
    def __init__(self, prompt_manager: OpenAIPromptManager):
        self.prompt_manager = prompt_manager
        self.performance_cache = {}
        self.cost_tracker = CostTracker()
        
    def optimize_model_selection(self, prompt: str, requirements: Dict) -> str:
        """Selecciona modelo óptimo basado en requisitos"""
        
        # Análisis de complejidad del prompt
        complexity_score = self.analyze_prompt_complexity(prompt)
        
        # Requerimientos de calidad vs costo
        quality_requirement = requirements.get("quality_level", "standard")
        cost_sensitivity = requirements.get("cost_sensitivity", "medium")
        latency_requirement = requirements.get("max_latency_ms", 5000)
        
        # Matriz de decisión
        if quality_requirement == "premium" and cost_sensitivity == "low":
            return "gpt-4o"
        elif complexity_score < 0.3 and cost_sensitivity == "high":
            return "gpt-4o-mini"
        elif requirements.get("multimodal", False):
            return "gpt-4o"
        else:
            return "gpt-4o"
    
    def optimize_parameters(self, prompt_type: str, context: Dict) -> Dict:
        """Optimiza parámetros basado en tipo de prompt y contexto"""
        
        optimization_profiles = {
            "creative_writing": {
                "temperature": 0.8,
                "top_p": 0.9,
                "frequency_penalty": 0.3,
                "presence_penalty": 0.2
            },
            "code_generation": {
                "temperature": 0.1,
                "top_p": 0.95,
                "frequency_penalty": 0.0,
                "presence_penalty": 0.0
            },
            "analysis_reasoning": {
                "temperature": 0.2,
                "top_p": 0.9,
                "frequency_penalty": 0.1,
                "presence_penalty": 0.1
            },
            "factual_qa": {
                "temperature": 0.0,
                "top_p": 0.9,
                "frequency_penalty": 0.0,
                "presence_penalty": 0.0
            }
        }
        
        base_params = optimization_profiles.get(prompt_type, optimization_profiles["analysis_reasoning"])
        
        # Ajustes dinámicos basados en contexto
        if context.get("domain_expertise_required", False):
            base_params["temperature"] *= 0.8
        
        if context.get("creativity_boost", False):
            base_params["temperature"] = min(base_params["temperature"] * 1.5, 1.0)
        
        return base_params
```

### 2. Anthropic Claude Platform

**Integración Profesional con Claude:**
```python
import anthropic
from typing import Dict, List, Optional

class ClaudePromptManager:
    def __init__(self, api_key: str):
        self.client = anthropic.Anthropic(api_key=api_key)
        self.model_configs = {
            "claude-3-5-opus": {
                "max_tokens": 4096,
                "temperature": 0.1,
                "capabilities": ["reasoning", "analysis", "creativity"]
            },
            "claude-3-5-sonnet": {
                "max_tokens": 4096,
                "temperature": 0.1,
                "capabilities": ["balanced", "cost_effective"]
            },
            "claude-3-5-haiku": {
                "max_tokens": 4096,
                "temperature": 0.1,
                "capabilities": ["speed", "efficiency"]
            }
        }
    
    def generate_with_claude(self, prompt: str, system_prompt: Optional[str] = None,
                           model: str = "claude-3-5-sonnet") -> Dict:
        """Genera respuesta usando Claude con optimizaciones específicas"""
        
        messages = [{"role": "user", "content": prompt}]
        
        try:
            response = self.client.messages.create(
                model=model,
                max_tokens=self.model_configs[model]["max_tokens"],
                temperature=self.model_configs[model]["temperature"],
                system=system_prompt,
                messages=messages
            )
            
            return {
                "content": response.content[0].text,
                "usage": {
                    "input_tokens": response.usage.input_tokens,
                    "output_tokens": response.usage.output_tokens
                },
                "model": model,
                "stop_reason": response.stop_reason
            }
            
        except Exception as e:
            raise Exception(f"Claude API error: {e}")
    
    def constitutional_ai_prompt(self, base_prompt: str, principles: List[str]) -> str:
        """Aplica principios de Constitutional AI a los prompts"""
        
        constitution = "\n".join([f"- {principle}" for principle in principles])
        
        constitutional_prompt = f"""
Please follow these constitutional principles when responding:

{constitution}

Ahora, por favor responde a la siguiente solicitud siguiendo estos principios:

{base_prompt}
"""
        return constitutional_prompt
    
    def chain_of_thought_claude(self, problem: str, thinking_steps: List[str]) -> str:
        """Optimiza Chain of Thought específicamente para Claude"""
        
        steps_text = "\n".join([f"{i+1}. {step}" for i, step in enumerate(thinking_steps)])
        
        cot_prompt = f"""
I need to solve this problem step by step. Let me think through this systematically:

Problem: {problem}

My thinking process:
{steps_text}

Let me work through each step carefully and provide my final answer.
"""
        return cot_prompt
```

### 3. Google Gemini Platform

**Integración con Gemini Pro:**
```python
import google.generativeai as genai
from typing import Dict, List, Optional

class GeminiPromptManager:
    def __init__(self, api_key: str):
        genai.configure(api_key=api_key)
        self.models = {
            "gemini-pro": genai.GenerativeModel('gemini-pro'),
            "gemini-pro-vision": genai.GenerativeModel('gemini-pro-vision')
        }
        
    def generate_with_gemini(self, prompt: str, model: str = "gemini-pro",
                           safety_settings: Optional[Dict] = None) -> Dict:
        """Genera respuesta con Gemini incluyendo configuraciones de seguridad"""
        
        model_instance = self.models.get(model)
        if not model_instance:
            raise ValueError(f"Unknown model: {model}")
        
        # Configuraciones de seguridad por defecto
        default_safety = {
            genai.types.HarmCategory.HARM_CATEGORY_HATE_SPEECH: genai.types.HarmBlockThreshold.BLOCK_MEDIUM_AND_ABOVE,
            genai.types.HarmCategory.HARM_CATEGORY_HARASSMENT: genai.types.HarmBlockThreshold.BLOCK_MEDIUM_AND_ABOVE,
            genai.types.HarmCategory.HARM_CATEGORY_SEXUALLY_EXPLICIT: genai.types.HarmBlockThreshold.BLOCK_MEDIUM_AND_ABOVE,
            genai.types.HarmCategory.HARM_CATEGORY_DANGEROUS_CONTENT: genai.types.HarmBlockThreshold.BLOCK_MEDIUM_AND_ABOVE,
        }
        
        safety_config = safety_settings or default_safety
        
        try:
            response = model_instance.generate_content(
                prompt,
                safety_settings=safety_config
            )
            
            return {
                "content": response.text,
                "safety_ratings": response.safety_ratings,
                "model": model,
                "finish_reason": response.finish_reason
            }
            
        except Exception as e:
            return {
                "error": str(e),
                "model": model,
                "prompt_blocked": True
            }
    
    def multimodal_prompt(self, text_prompt: str, image_data: bytes,
                         mime_type: str = "image/jpeg") -> Dict:
        """Maneja prompts multimodales con imágenes"""
        
        image_part = {
            "mime_type": mime_type,
            "data": image_data
        }
        
        model = self.models["gemini-pro-vision"]
        
        try:
            response = model.generate_content([text_prompt, image_part])
            
            return {
                "content": response.text,
                "safety_ratings": response.safety_ratings,
                "model": "gemini-pro-vision",
                "multimodal": True
            }
            
        except Exception as e:
            return {
                "error": str(e),
                "model": "gemini-pro-vision",
                "multimodal": True
            }
```

## Proveedores Adicionales y Plataformas Especializadas

### 4. Azure OpenAI Service

**Integración Empresarial con Azure:**
```python
import openai
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

class AzureOpenAIManager:
    def __init__(self, endpoint: str, deployment_name: str, api_version: str = "2024-02-01"):
        self.client = openai.AzureOpenAI(
            azure_endpoint=endpoint,
            api_key=self.get_api_key_from_keyvault(),
            api_version=api_version
        )
        self.deployment_name = deployment_name
        self.compliance_tracker = ComplianceTracker()
        
    def get_api_key_from_keyvault(self) -> str:
        """Obtiene API key de Azure Key Vault de forma segura"""
        credential = DefaultAzureCredential()
        vault_url = "https://your-keyvault.vault.azure.net/"
        client = SecretClient(vault_url=vault_url, credential=credential)
        return client.get_secret("openai-api-key").value
    
    def generate_with_compliance(self, messages: List[Dict], 
                               data_classification: str = "internal") -> Dict:
        """Genera respuesta con tracking de compliance"""
        
        # Validaciones de compliance
        if data_classification == "confidential" and not self.compliance_tracker.is_pii_safe():
            raise Exception("PII detected in confidential data classification")
        
        # Log para auditoría
        audit_id = self.compliance_tracker.log_request(messages, data_classification)
        
        try:
            response = self.client.chat.completions.create(
                model=self.deployment_name,
                messages=messages,
                temperature=0.1,
                max_tokens=1000
            )
            
            # Log de respuesta para compliance
            self.compliance_tracker.log_response(audit_id, response)
            
            return {
                "content": response.choices[0].message.content,
                "usage": response.usage.model_dump(),
                "audit_id": audit_id,
                "compliance_status": "compliant"
            }
            
        except Exception as e:
            self.compliance_tracker.log_error(audit_id, str(e))
            raise
```

### 5. AWS Bedrock

**Integración con Múltiples Modelos en Bedrock:**
```python
import boto3
import json
from typing import Dict, List, Optional

class BedrockMultiModelManager:
    def __init__(self, region_name: str = "us-east-1"):
        self.client = boto3.client("bedrock-runtime", region_name=region_name)
        self.model_configs = {
            "anthropic.claude-3-5-sonnet-20240620-v1:0": {
                "provider": "anthropic",
                "capabilities": ["reasoning", "analysis", "safety"]
            },
            "amazon.titan-text-premier-v1:0": {
                "provider": "amazon",
                "capabilities": ["general", "summarization"]
            },
            "meta.llama2-70b-chat-v1": {
                "provider": "meta",
                "capabilities": ["chat", "code", "opensource"]
            }
        }
    
    def invoke_model(self, model_id: str, prompt: str, parameters: Dict = None) -> Dict:
        """Invoca modelo específico en Bedrock"""
        
        default_params = {
            "max_tokens": 1000,
            "temperature": 0.1,
            "top_p": 0.9
        }
        
        if parameters:
            default_params.update(parameters)
        
        # Preparar payload según el proveedor
        if model_id.startswith("anthropic"):
            body = {
                "anthropic_version": "bedrock-2023-05-31",
                "max_tokens": default_params["max_tokens"],
                "temperature": default_params["temperature"],
                "messages": [{"role": "user", "content": prompt}]
            }
        elif model_id.startswith("amazon"):
            body = {
                "inputText": prompt,
                "textGenerationConfig": default_params
            }
        elif model_id.startswith("meta"):
            body = {
                "prompt": prompt,
                "max_gen_len": default_params["max_tokens"],
                "temperature": default_params["temperature"],
                "top_p": default_params["top_p"]
            }
        else:
            raise ValueError(f"Unsupported model: {model_id}")
        
        try:
            response = self.client.invoke_model(
                modelId=model_id,
                body=json.dumps(body),
                contentType="application/json",
                accept="application/json"
            )
            
            response_body = json.loads(response["body"].read())
            
            # Procesar respuesta según proveedor
            if model_id.startswith("anthropic"):
                content = response_body["content"][0]["text"]
                usage = response_body["usage"]
            elif model_id.startswith("amazon"):
                content = response_body["results"][0]["outputText"]
                usage = {"inputTextTokenCount": response_body["inputTextTokenCount"]}
            elif model_id.startswith("meta"):
                content = response_body["generation"]
                usage = {"prompt_token_count": response_body["prompt_token_count"]}
            
            return {
                "content": content,
                "usage": usage,
                "model": model_id,
                "provider": self.model_configs[model_id]["provider"]
            }
            
        except Exception as e:
            return {
                "error": str(e),
                "model": model_id
            }
    
    def smart_model_selection(self, prompt: str, requirements: Dict) -> str:
        """Selecciona modelo óptimo basado en requisitos"""
        
        use_case = requirements.get("use_case", "general")
        cost_sensitivity = requirements.get("cost_sensitivity", "medium")
        compliance_level = requirements.get("compliance", "standard")
        
        # Lógica de selección inteligente
        if use_case == "reasoning" and compliance_level == "high":
            return "anthropic.claude-3-5-sonnet-20240229-v1:0"
        elif cost_sensitivity == "high":
            return "amazon.titan-text-premier-v1:0"
        elif use_case == "code":
            return "meta.llama2-70b-chat-v1"
        else:
            return "anthropic.claude-3-5-sonnet-20240229-v1:0"
```

### 6. HuggingFace Hub

**Integración con Modelos Open Source:**
```python
from transformers import pipeline, AutoTokenizer, AutoModelForCausalLM
import torch
from typing import Dict, List, Optional

class HuggingFaceModelManager:
    def __init__(self):
        self.loaded_models = {}
        self.device = "cuda" if torch.cuda.is_available() else "cpu"
        
    def load_model(self, model_name: str, task: str = "text-generation") -> None:
        """Carga modelo desde HuggingFace Hub"""
        
        if model_name not in self.loaded_models:
            try:
                if task == "text-generation":
                    tokenizer = AutoTokenizer.from_pretrained(model_name)
                    model = AutoModelForCausalLM.from_pretrained(
                        model_name,
                        torch_dtype=torch.float16 if self.device == "cuda" else torch.float32,
                        device_map="auto" if self.device == "cuda" else None
                    )
                    
                    self.loaded_models[model_name] = {
                        "model": model,
                        "tokenizer": tokenizer,
                        "pipeline": pipeline(
                            task,
                            model=model,
                            tokenizer=tokenizer,
                            device=0 if self.device == "cuda" else -1
                        )
                    }
                else:
                    self.loaded_models[model_name] = {
                        "pipeline": pipeline(task, model=model_name)
                    }
                    
                print(f"Model {model_name} loaded successfully")
                
            except Exception as e:
                raise Exception(f"Failed to load model {model_name}: {e}")
    
    def generate_text(self, model_name: str, prompt: str, **kwargs) -> Dict:
        """Genera texto usando modelo específico"""
        
        if model_name not in self.loaded_models:
            self.load_model(model_name)
        
        model_info = self.loaded_models[model_name]
        pipe = model_info["pipeline"]
        
        # Parámetros por defecto
        generation_params = {
            "max_new_tokens": 512,
            "temperature": 0.7,
            "do_sample": True,
            "top_p": 0.9,
            "repetition_penalty": 1.1
        }
        generation_params.update(kwargs)
        
        try:
            start_time = time.time()
            result = pipe(prompt, **generation_params)
            end_time = time.time()
            
            return {
                "content": result[0]["generated_text"][len(prompt):].strip(),
                "model": model_name,
                "execution_time": end_time - start_time,
                "parameters": generation_params
            }
            
        except Exception as e:
            return {
                "error": str(e),
                "model": model_name
            }
    
    def batch_generate(self, model_name: str, prompts: List[str], **kwargs) -> List[Dict]:
        """Genera texto para múltiples prompts en batch"""
        
        if model_name not in self.loaded_models:
            self.load_model(model_name)
        
        results = []
        for prompt in prompts:
            result = self.generate_text(model_name, prompt, **kwargs)
            results.append(result)
        
        return results
```

## Herramientas de Desarrollo y Testing

### 1. Prompt Testing Frameworks

**Framework Comprehensivo de Testing:**
```python
class PromptTestFramework:
    def __init__(self):
        self.test_suites = {}
        self.evaluation_metrics = {
            "accuracy": AccuracyEvaluator(),
            "relevance": RelevanceEvaluator(),
            "coherence": CoherenceEvaluator(),
            "safety": SafetyEvaluator(),
            "bias": BiasEvaluator()
        }
        self.benchmark_datasets = BenchmarkDataLoader()
        
    def create_test_suite(self, suite_name: str, test_cases: List[Dict]) -> None:
        """Crea suite de pruebas para prompts específicos"""
        
        validated_cases = []
        for test_case in test_cases:
            if self.validate_test_case(test_case):
                validated_cases.append(test_case)
            else:
                raise ValueError(f"Invalid test case: {test_case}")
        
        self.test_suites[suite_name] = {
            "test_cases": validated_cases,
            "created_at": datetime.utcnow(),
            "status": "active"
        }
    
    def run_comprehensive_evaluation(self, prompt_template: str, 
                                   test_suite_name: str,
                                   model_configs: List[Dict]) -> Dict:
        """Ejecuta evaluación comprensiva del prompt"""
        
        if test_suite_name not in self.test_suites:
            raise ValueError(f"Test suite not found: {test_suite_name}")
        
        test_suite = self.test_suites[test_suite_name]
        evaluation_results = {
            "prompt_template": prompt_template,
            "test_suite": test_suite_name,
            "model_results": {},
            "aggregate_metrics": {},
            "recommendations": []
        }
        
        # Ejecutar para cada configuración de modelo
        for model_config in model_configs:
            model_id = f"{model_config['provider']}_{model_config['model']}"
            model_results = self.evaluate_model_config(
                prompt_template, test_suite["test_cases"], model_config
            )
            evaluation_results["model_results"][model_id] = model_results
        
        # Agregar métricas entre modelos
        evaluation_results["aggregate_metrics"] = self.aggregate_model_metrics(
            evaluation_results["model_results"]
        )
        
        # Generar recomendaciones
        evaluation_results["recommendations"] = self.generate_optimization_recommendations(
            evaluation_results["aggregate_metrics"]
        )
        
        return evaluation_results
    
    def evaluate_model_config(self, prompt_template: str, test_cases: List[Dict],
                            model_config: Dict) -> Dict:
        """Evalúa prompt con configuración específica de modelo"""
        
        results = {
            "model_config": model_config,
            "test_results": [],
            "metrics": {},
            "performance_stats": {}
        }
        
        start_time = time.time()
        
        for test_case in test_cases:
            # Renderizar prompt con datos del test case
            rendered_prompt = self.render_prompt_template(
                prompt_template, test_case["input_data"]
            )
            
            # Generar respuesta
            response = self.generate_with_model(rendered_prompt, model_config)
            
            # Evaluar respuesta
            evaluation = self.evaluate_response(
                test_case, response, model_config
            )
            
            results["test_results"].append({
                "test_case_id": test_case["id"],
                "input": test_case["input_data"],
                "expected": test_case.get("expected_output"),
                "actual": response,
                "evaluation": evaluation
            })
        
        # Calcular métricas agregadas
        results["metrics"] = self.calculate_aggregate_metrics(results["test_results"])
        
        # Estadísticas de rendimiento
        results["performance_stats"] = {
            "total_time": time.time() - start_time,
            "avg_response_time": sum(r["evaluation"]["response_time"] 
                                   for r in results["test_results"]) / len(results["test_results"]),
            "success_rate": sum(1 for r in results["test_results"] 
                              if r["evaluation"]["success"]) / len(results["test_results"])
        }
        
        return results
```

### 2. A/B Testing para Prompts

**Framework de Experimentación:**
```python
class PromptABTestFramework:
    def __init__(self):
        self.experiments = {}
        self.traffic_splitter = TrafficSplitter()
        self.metrics_collector = MetricsCollector()
        self.statistical_analyzer = StatisticalAnalyzer()
        
    def create_experiment(self, experiment_name: str, 
                         prompt_variants: List[Dict],
                         traffic_allocation: Dict,
                         success_metrics: List[str],
                         minimum_sample_size: int = 1000) -> str:
        """Crea experimento A/B para prompts"""
        
        experiment_id = self.generate_experiment_id(experiment_name)
        
        # Validar configuración
        if sum(traffic_allocation.values()) != 1.0:
            raise ValueError("Traffic allocation must sum to 1.0")
        
        if len(prompt_variants) != len(traffic_allocation):
            raise ValueError("Number of variants must match traffic allocation")
        
        self.experiments[experiment_id] = {
            "name": experiment_name,
            "variants": prompt_variants,
            "traffic_allocation": traffic_allocation,
            "success_metrics": success_metrics,
            "minimum_sample_size": minimum_sample_size,
            "status": "active",
            "created_at": datetime.utcnow(),
            "results": {"samples": {}, "metrics": {}}
        }
        
        return experiment_id
    
    def execute_experiment_request(self, experiment_id: str, 
                                 user_context: Dict, 
                                 input_data: Dict) -> Dict:
        """Ejecuta request dentro del experimento A/B"""
        
        if experiment_id not in self.experiments:
            raise ValueError(f"Experiment not found: {experiment_id}")
        
        experiment = self.experiments[experiment_id]
        
        # Determinar variante para el usuario
        variant = self.traffic_splitter.assign_variant(
            user_context, experiment["traffic_allocation"]
        )
        
        # Ejecutar prompt de la variante asignada
        prompt_config = next(v for v in experiment["variants"] if v["name"] == variant)
        
        start_time = time.time()
        response = self.execute_prompt_variant(prompt_config, input_data)
        execution_time = time.time() - start_time
        
        # Registrar métricas
        self.record_experiment_interaction(
            experiment_id, variant, user_context, input_data, response, execution_time
        )
        
        return {
            "response": response,
            "variant": variant,
            "experiment_id": experiment_id,
            "execution_time": execution_time
        }
    
    def analyze_experiment_results(self, experiment_id: str) -> Dict:
        """Analiza resultados estadísticos del experimento"""
        
        experiment = self.experiments[experiment_id]
        
        # Verificar tamaño mínimo de muestra
        total_samples = sum(len(samples) for samples in experiment["results"]["samples"].values())
        if total_samples < experiment["minimum_sample_size"]:
            return {
                "status": "insufficient_data",
                "current_samples": total_samples,
                "required_samples": experiment["minimum_sample_size"]
            }
        
        # Análisis estadístico
        statistical_results = {}
        for metric in experiment["success_metrics"]:
            metric_analysis = self.statistical_analyzer.analyze_metric(
                experiment["results"]["metrics"], metric
            )
            statistical_results[metric] = metric_analysis
        
        # Determinación del ganador
        winner_analysis = self.determine_experiment_winner(statistical_results)
        
        return {
            "experiment_id": experiment_id,
            "status": "analysis_complete",
            "total_samples": total_samples,
            "statistical_results": statistical_results,
            "winner": winner_analysis,
            "recommendations": self.generate_experiment_recommendations(winner_analysis)
        }
```

### 3. Deployment y Versionado

**Sistema de Deployment Continuo:**
```python
class PromptDeploymentManager:
    def __init__(self):
        self.version_control = PromptVersionControl()
        self.deployment_pipeline = DeploymentPipeline()
        self.rollback_manager = RollbackManager()
        self.monitoring_system = PromptMonitoring()
        
    def deploy_prompt_version(self, prompt_id: str, version: str,
                            deployment_config: Dict) -> Dict:
        """Despliega versión específica de prompt con configuración"""
        
        # Validar versión
        if not self.version_control.version_exists(prompt_id, version):
            raise ValueError(f"Version {version} not found for prompt {prompt_id}")
        
        # Obtener artefactos de la versión
        prompt_artifacts = self.version_control.get_version_artifacts(prompt_id, version)
        
        # Validaciones pre-deployment
        validation_results = self.run_pre_deployment_validations(
            prompt_artifacts, deployment_config
        )
        
        if not validation_results["passed"]:
            raise Exception(f"Pre-deployment validation failed: {validation_results['errors']}")
        
        # Estrategia de deployment
        deployment_strategy = deployment_config.get("strategy", "blue_green")
        
        if deployment_strategy == "blue_green":
            result = self.blue_green_deployment(prompt_artifacts, deployment_config)
        elif deployment_strategy == "canary":
            result = self.canary_deployment(prompt_artifacts, deployment_config)
        elif deployment_strategy == "rolling":
            result = self.rolling_deployment(prompt_artifacts, deployment_config)
        else:
            raise ValueError(f"Unknown deployment strategy: {deployment_strategy}")
        
        # Configurar monitoreo post-deployment
        self.monitoring_system.setup_monitoring(prompt_id, version, deployment_config)
        
        return result
    
    def blue_green_deployment(self, prompt_artifacts: Dict, config: Dict) -> Dict:
        """Ejecuta deployment blue-green"""
        
        deployment_id = self.generate_deployment_id()
        
        try:
            # Preparar entorno green
            green_environment = self.deployment_pipeline.prepare_green_environment(
                prompt_artifacts, config
            )
            
            # Ejecutar tests de smoke en green
            smoke_test_results = self.run_smoke_tests(green_environment)
            
            if not smoke_test_results["passed"]:
                raise Exception(f"Smoke tests failed: {smoke_test_results['errors']}")
            
            # Switch traffic a green
            traffic_switch_result = self.deployment_pipeline.switch_traffic_to_green(
                green_environment, config.get("traffic_percentage", 100)
            )
            
            # Monitoreo post-switch
            monitoring_setup = self.setup_post_deployment_monitoring(
                deployment_id, green_environment
            )
            
            return {
                "deployment_id": deployment_id,
                "strategy": "blue_green",
                "status": "success",
                "green_environment": green_environment,
                "traffic_switch": traffic_switch_result,
                "monitoring": monitoring_setup
            }
            
        except Exception as e:
            # Rollback automático en caso de error
            self.rollback_manager.initiate_automatic_rollback(deployment_id, str(e))
            raise Exception(f"Blue-green deployment failed: {e}")
    
    def canary_deployment(self, prompt_artifacts: Dict, config: Dict) -> Dict:
        """Ejecuta deployment canary progresivo"""
        
        deployment_id = self.generate_deployment_id()
        canary_stages = config.get("canary_stages", [10, 25, 50, 100])
        
        deployment_results = {
            "deployment_id": deployment_id,
            "strategy": "canary",
            "stages": []
        }
        
        for stage_percentage in canary_stages:
            stage_result = self.execute_canary_stage(
                deployment_id, prompt_artifacts, stage_percentage, config
            )
            
            deployment_results["stages"].append(stage_result)
            
            # Evaluación de métricas de salud
            health_check = self.evaluate_canary_health(deployment_id, stage_percentage)
            
            if not health_check["healthy"]:
                # Rollback inmediato
                self.rollback_manager.rollback_canary_deployment(deployment_id)
                deployment_results["status"] = "failed"
                deployment_results["failure_reason"] = health_check["issues"]
                return deployment_results
            
            # Pausa entre etapas para observación
            time.sleep(config.get("stage_wait_time", 300))  # 5 minutos por defecto
        
        deployment_results["status"] = "success"
        return deployment_results
```

## Herramientas de Monitoreo y Observabilidad

### 1. Sistema de Métricas en Tiempo Real

**Dashboard de Observabilidad:**
```python
class PromptObservabilityPlatform:
    def __init__(self):
        self.metrics_store = MetricsStore()
        self.alerting_system = AlertingSystem()
        self.trace_collector = TraceCollector()
        self.anomaly_detector = AnomalyDetector()
        
    def track_prompt_execution(self, prompt_id: str, execution_context: Dict,
                             response_data: Dict) -> None:
        """Rastrea ejecución de prompt para observabilidad"""
        
        timestamp = datetime.utcnow()
        trace_id = self.generate_trace_id()
        
        # Métricas básicas
        basic_metrics = {
            "prompt_id": prompt_id,
            "trace_id": trace_id,
            "timestamp": timestamp,
            "latency_ms": execution_context.get("execution_time") * 1000,
            "token_usage": response_data.get("usage", {}),
            "model_used": execution_context.get("model"),
            "success": response_data.get("success", True)
        }
        
        # Métricas de calidad
        quality_metrics = self.analyze_response_quality(response_data)
        
        # Métricas de costo
        cost_metrics = self.calculate_execution_cost(
            response_data.get("usage", {}), execution_context.get("model")
        )
        
        # Consolidar métricas
        all_metrics = {**basic_metrics, **quality_metrics, **cost_metrics}
        
        # Almacenar métricas
        self.metrics_store.store_metrics(all_metrics)
        
        # Detectar anomalías
        anomalies = self.anomaly_detector.detect_anomalies(all_metrics)
        if anomalies:
            self.alerting_system.trigger_anomaly_alert(prompt_id, anomalies)
        
        # Verificar SLA
        sla_violations = self.check_sla_violations(all_metrics)
        if sla_violations:
            self.alerting_system.trigger_sla_alert(prompt_id, sla_violations)
    
    def generate_performance_report(self, prompt_id: str, time_period: Dict) -> Dict:
        """Genera reporte de rendimiento para período específico"""
        
        # Obtener métricas del período
        period_metrics = self.metrics_store.query_metrics(
            prompt_id, time_period["start"], time_period["end"]
        )
        
        # Análisis estadístico
        statistical_analysis = {
            "total_requests": len(period_metrics),
            "success_rate": sum(1 for m in period_metrics if m["success"]) / len(period_metrics),
            "latency_percentiles": self.calculate_latency_percentiles(period_metrics),
            "token_usage_stats": self.analyze_token_usage(period_metrics),
            "cost_analysis": self.analyze_costs(period_metrics)
        }
        
        # Análisis de tendencias
        trend_analysis = self.analyze_performance_trends(period_metrics)
        
        # Detección de patrones
        pattern_analysis = self.detect_usage_patterns(period_metrics)
        
        return {
            "prompt_id": prompt_id,
            "time_period": time_period,
            "statistical_analysis": statistical_analysis,
            "trend_analysis": trend_analysis,
            "pattern_analysis": pattern_analysis,
            "recommendations": self.generate_optimization_recommendations(statistical_analysis)
        }
```

### 2. Sistema de Alertas Inteligentes

**Alerting Avanzado:**
```python
class IntelligentAlertingSystem:
    def __init__(self):
        self.alert_rules = AlertRuleEngine()
        self.notification_channels = NotificationChannelManager()
        self.escalation_policies = EscalationPolicyManager()
        self.alert_correlation = AlertCorrelationEngine()
        
    def configure_prompt_alerts(self, prompt_id: str, alert_config: Dict) -> None:
        """Configura alertas específicas para un prompt"""
        
        # Alertas de rendimiento
        performance_alerts = [
            {
                "name": f"{prompt_id}_high_latency",
                "condition": "avg(latency_ms) > {threshold}".format(
                    threshold=alert_config.get("max_latency_ms", 5000)
                ),
                "window": "5m",
                "severity": "warning"
            },
            {
                "name": f"{prompt_id}_low_success_rate",
                "condition": "avg(success_rate) < {threshold}".format(
                    threshold=alert_config.get("min_success_rate", 0.95)
                ),
                "window": "10m",
                "severity": "critical"
            }
        ]
        
        # Alertas de costo
        cost_alerts = [
            {
                "name": f"{prompt_id}_high_cost",
                "condition": "sum(cost_usd) > {threshold}".format(
                    threshold=alert_config.get("max_hourly_cost", 100)
                ),
                "window": "1h",
                "severity": "warning"
            }
        ]
        
        # Alertas de calidad
        quality_alerts = [
            {
                "name": f"{prompt_id}_quality_degradation",
                "condition": "avg(quality_score) < {threshold}".format(
                    threshold=alert_config.get("min_quality_score", 0.8)
                ),
                "window": "15m",
                "severity": "warning"
            }
        ]
        
        all_alerts = performance_alerts + cost_alerts + quality_alerts
        
        for alert_config in all_alerts:
            self.alert_rules.create_rule(prompt_id, alert_config)
    
    def process_incoming_alert(self, alert: Dict) -> None:
        """Procesa alerta entrante con lógica de correlación e escalamiento"""
        
        # Correlacionar con alertas existentes
        correlated_alerts = self.alert_correlation.correlate_alert(alert)
        
        if correlated_alerts:
            # Actualizar alerta existente en lugar de crear nueva
            self.alert_correlation.update_correlated_alert(alert, correlated_alerts)
        else:
            # Nueva alerta - procesar normalmente
            self.process_new_alert(alert)
    
    def process_new_alert(self, alert: Dict) -> None:
        """Procesa nueva alerta con escalamiento inteligente"""
        
        # Determinar política de escalamiento
        escalation_policy = self.escalation_policies.get_policy(
            alert["prompt_id"], alert["severity"]
        )
        
        # Notificación inicial
        self.notification_channels.send_alert_notification(alert, escalation_policy["initial"])
        
        # Programar escalamientos
        if escalation_policy.get("escalation_steps"):
            self.schedule_alert_escalations(alert, escalation_policy["escalation_steps"])
        
        # Auto-resolución si es aplicable
        if alert.get("auto_resolve_conditions"):
            self.schedule_auto_resolution_check(alert)
```

## Frameworks de Integración Empresarial

### 1. API Gateway Pattern

Permite centralizar el acceso, aplicar autenticación, rate limiting y logging para todas las llamadas a LLMs.

```python
class PromptAPIGateway:
    def __init__(self, providers: Dict):
        self.providers = providers  # {'openai': OpenAIPromptManager(...), ...}
        self.auth_manager = AuthManager()
        self.logger = APILogger()
        self.rate_limiter = RateLimiter()
    
    def handle_request(self, user_token: str, provider: str, payload: Dict) -> Dict:
        if not self.auth_manager.validate(user_token):
            return {"error": "Unauthorized"}
        if not self.rate_limiter.allow(user_token):
            return {"error": "Rate limit exceeded"}
        self.logger.log_request(user_token, provider, payload)
        manager = self.providers.get(provider)
        if not manager:
            return {"error": "Provider not supported"}
        return manager.generate_with_retry(**payload)
```

### 2. Orquestación Multi-Modelo

Permite seleccionar dinámicamente el mejor modelo según contexto, costo, latencia o requisitos regulatorios.

```python
class MultiModelOrchestrator:
    def __init__(self, managers: Dict):
        self.managers = managers  # {'openai': ..., 'claude': ..., 'gemini': ...}
    
    def route_prompt(self, prompt: str, context: Dict) -> Dict:
        # Ejemplo de lógica de selección
        if context.get('multimodal'):
            return self.managers['gemini'].generate_with_gemini(prompt, model='gemini-pro-vision')
        if context.get('compliance') == 'EU':
            return self.managers['claude'].generate_with_claude(prompt, model='claude-3-5-sonnet')
        if context.get('cost_sensitivity') == 'high':
            return self.managers['openai'].generate_with_retry([{"role": "user", "content": prompt}], model='gpt-4o-mini')
        return self.managers['openai'].generate_with_retry([{"role": "user", "content": prompt}], model='gpt-4o')
```

## Mejores Prácticas de Seguridad y Compliance

- **Gestión de claves y secretos:** Usar vaults y rotación periódica.
- **Auditoría y logging:** Registrar cada request y respuesta para trazabilidad.
- **Rate limiting y throttling:** Proteger contra abuso y picos de tráfico.
- **Validación de inputs:** Sanitizar y validar todos los datos antes de enviarlos al modelo.
- **Cumplimiento normativo:** Asegurar GDPR, SOC2, HIPAA según el sector y la región.
- **Redacción de prompts seguros:** Evitar inyecciones y fugas de información sensible.

## Tabla Comparativa de Proveedores LLM

| Proveedor   | Modelos Destacados      | Multimodal | Latencia | Costo | Seguridad | Compliance | Casos de Uso Clave           |
|-------------|------------------------|------------|----------|-------|-----------|------------|------------------------------|
| OpenAI      | GPT-4o, GPT-4o-mini     | Sí         | Media    | $$    | Avanzada  | SOC2, GDPR | General, código, análisis    |
| Anthropic   | Claude 3.5 Opus/Sonnet | No         | Baja     | $$$   | Alta      | GDPR       | Razonamiento, compliance     |
| Google      | Gemini Pro/Vision      | Sí         | Baja     | $$    | Alta      | SOC2, GDPR | Multimodal, enterprise       |
| Mistral     | Mistral Large/Small    | No         | Muy baja | $     | Media     | GDPR       | Open source, on-prem         |
| Cohere      | Command, Embed         | No         | Media    | $$    | Alta      | SOC2       | Clasificación, embeddings     |
| AWS Bedrock | Titan, Claude, Llama   | Sí         | Variable | $$    | Alta      | HIPAA, GDPR| Integración cloud, enterprise |
| Azure       | OpenAI Service         | Sí         | Baja     | $$    | Alta      | SOC2, GDPR | Integración cloud, enterprise |
| HuggingFace | Varios (open source)   | No         | Variable | $     | Media     | GDPR       | Modelos personalizados, código|

**Notas:**
- *Latencia y costo pueden variar según región y volumen.*
- *La elección óptima depende de requisitos de negocio, privacidad y presupuesto.*

## Patrones de Versionado y Rollback

- **Versionado semántico de prompts:** `prompt_v1.2.0.md`
- **Pruebas automatizadas antes de deploy:** Usar frameworks de testing y A/B.
- **Rollback automático:** Mantener versiones previas listas para restaurar.
- **Monitoreo post-deploy:** Alertas ante degradación de calidad o performance.

## Recursos y Enlaces Útiles

- [OpenAI API Docs](https://platform.openai.com/docs)
- [Anthropic Claude API](https://docs.anthropic.com/claude/docs)
- [Google Gemini API](https://ai.google.dev/gemini-api/docs)
- [Mistral AI](https://docs.mistral.ai/)
- [Cohere API](https://docs.cohere.com/)
- [AWS Bedrock](https://docs.aws.amazon.com/bedrock/)
- [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/)

---

Esta sección proporciona una visión integral y práctica para seleccionar, integrar y operar plataformas LLM de manera profesional y segura en entornos empresariales.

## Herramientas Especializadas de Prompt Engineering

### 1. PromptFoo - Framework de Testing

**Configuración y uso de PromptFoo:**
```yaml
# promptfoo.yaml
description: 'Comprehensive prompt evaluation'

prompts:
  - 'You are an expert business analyst. Analyze this company: {{company_data}}'
  - 'As a senior consultant, provide strategic recommendations for: {{company_data}}'
  - 'Perform a SWOT analysis for the following company: {{company_data}}'

providers:
  - openai:gpt-4o
  - anthropic:claude-3-5-sonnet
  - openai:gpt-4o-mini

tests:
  - vars:
      company_data: 'TechCorp: SaaS platform, $10M ARR, 45% YoY growth, Series B'
    assert:
      - type: contains
        value: 'revenue'
      - type: contains
        value: 'growth'
      - type: llm-rubric
        value: 'Does the analysis include market positioning assessment?'

  - vars:
      company_data: 'ManufacturingCo: $50M revenue, 15% EBITDA, declining market'
    assert:
      - type: contains
        value: 'EBITDA'
      - type: llm-rubric
        value: 'Does the response address the declining market challenge?'
```

### 2. LangSmith - Observabilidad Avanzada

**Integración con LangSmith:**
```python
from langsmith import Client
from langsmith.wrappers import wrap_openai
import openai

class LangSmithPromptTracker:
    def __init__(self, api_key: str, project_name: str):
        self.client = Client(api_key=api_key)
        self.project_name = project_name
        
        # Wrap OpenAI client para tracking automático
        self.openai_client = wrap_openai(openai.OpenAI())
        
    def track_prompt_execution(self, prompt_name: str, inputs: Dict, 
                             prompt_template: str) -> Dict:
        """Ejecuta y trackea prompt con metadata completa"""
        
        with self.client.tracing_context(
            project_name=self.project_name,
            metadata={
                "prompt_name": prompt_name,
                "version": "1.0",
                "environment": "production"
            }
        ):
            try:
                response = self.openai_client.chat.completions.create(
                    model="gpt-4o",
                    messages=[
                        {"role": "system", "content": prompt_template},
                        {"role": "user", "content": str(inputs)}
                    ]
                )
                
                # LangSmith automáticamente captura inputs/outputs
                return {
                    "content": response.choices[0].message.content,
                    "usage": response.usage.model_dump(),
                    "trace_id": self.client.get_current_run_tree().id
                }
                
            except Exception as e:
                # Error también es trackeado automáticamente
                raise
    
    def create_dataset(self, dataset_name: str, examples: List[Dict]) -> str:
        """Crea dataset para evaluación sistemática"""
        
        dataset = self.client.create_dataset(
            dataset_name=dataset_name,
            description="Evaluation dataset for prompt optimization"
        )
        
        for example in examples:
            self.client.create_example(
                dataset_id=dataset.id,
                inputs=example["inputs"],
                outputs=example["expected_outputs"],
                metadata=example.get("metadata", {})
            )
        
        return dataset.id
    
    def run_evaluation(self, dataset_id: str, prompt_variants: List[Dict]) -> Dict:
        """Ejecuta evaluación sistemática contra dataset"""
        
        evaluation_results = {}
        
        for variant in prompt_variants:
            variant_name = variant["name"]
            variant_template = variant["template"]
            
            # Ejecutar evaluación
            results = self.client.run_on_dataset(
                dataset_name=dataset_id,
                llm_or_chain_factory=lambda: self.create_chain(variant_template),
                project_name=f"{self.project_name}_eval_{variant_name}"
            )
            
            evaluation_results[variant_name] = results
        
        return evaluation_results
```

### 3. Weights & Biases - Experimentación ML

**Tracking de experimentos de prompts:**
```python
import wandb
from typing import Dict, List, Optional

class WandBPromptExperimentTracker:
    def __init__(self, project_name: str, entity: Optional[str] = None):
        self.project_name = project_name
        self.entity = entity
        
    def start_experiment(self, experiment_name: str, config: Dict) -> None:
        """Inicia experimento de prompt con configuración"""
        
        wandb.init(
            project=self.project_name,
            entity=self.entity,
            name=experiment_name,
            config=config,
            tags=["prompt-engineering", "llm-evaluation"]
        )
        
    def log_prompt_performance(self, prompt_variant: str, metrics: Dict) -> None:
        """Registra métricas de rendimiento del prompt"""
        
        wandb.log({
            "prompt_variant": prompt_variant,
            **metrics
        })
        
    def log_prompt_comparison(self, variants: List[Dict]) -> None:
        """Registra comparación entre variantes de prompt"""
        
        # Crear tabla comparativa
        table = wandb.Table(
            columns=["variant", "accuracy", "relevance", "coherence", "cost", "latency"]
        )
        
        for variant in variants:
            table.add_data(
                variant["name"],
                variant["metrics"]["accuracy"],
                variant["metrics"]["relevance"],
                variant["metrics"]["coherence"],
                variant["metrics"]["cost"],
                variant["metrics"]["latency"]
            )
        
        wandb.log({"prompt_comparison": table})
        
    def track_hyperparameter_sweep(self, sweep_config: Dict) -> str:
        """Configura sweep de hiperparámetros para prompts"""
        
        sweep_id = wandb.sweep(
            sweep=sweep_config,
            project=self.project_name,
            entity=self.entity
        )
        
        return sweep_id
    
    def run_sweep_agent(self, sweep_id: str, function: callable) -> None:
        """Ejecuta agente de sweep"""
        
        wandb.agent(sweep_id, function=function, count=50)

# Ejemplo de configuración de sweep
sweep_config = {
    'method': 'bayes',
    'metric': {
        'name': 'overall_score',
        'goal': 'maximize'
    },
    'parameters': {
        'temperature': {
            'min': 0.0,
            'max': 1.0
        },
        'max_tokens': {
            'values': [512, 1024, 2048, 4096]
        },
        'prompt_style': {
            'values': ['direct', 'step_by_step', 'examples', 'chain_of_thought']
        }
    }
}
```

## Frameworks de Evaluación Especializada

### 1. BLEU, ROUGE y BERTScore

**Métricas automáticas de evaluación:**
```python
from rouge_score import rouge_scorer
from bert_score import score
import nltk
from nltk.translate.bleu_score import sentence_bleu
import numpy as np

class AutomaticEvaluationFramework:
    def __init__(self):
        self.rouge_scorer = rouge_scorer.RougeScorer(['rouge1', 'rouge2', 'rougeL'], use_stemmer=True)
        nltk.download('punkt', quiet=True)
        
    def evaluate_response(self, generated: str, reference: str) -> Dict:
        """Evalúa respuesta generada contra referencia usando múltiples métricas"""
        
        # ROUGE scores
        rouge_scores = self.rouge_scorer.score(reference, generated)
        
        # BLEU score
        reference_tokens = nltk.word_tokenize(reference.lower())
        generated_tokens = nltk.word_tokenize(generated.lower())
        bleu_score = sentence_bleu([reference_tokens], generated_tokens)
        
        # BERTScore
        P, R, F1 = score([generated], [reference], lang="en", verbose=False)
        bert_score = F1.mean().item()
        
        return {
            "rouge1_f": rouge_scores['rouge1'].fmeasure,
            "rouge2_f": rouge_scores['rouge2'].fmeasure,
            "rougeL_f": rouge_scores['rougeL'].fmeasure,
            "bleu": bleu_score,
            "bert_score": bert_score,
            "overall_score": np.mean([
                rouge_scores['rouge1'].fmeasure,
                rouge_scores['rouge2'].fmeasure,
                rouge_scores['rougeL'].fmeasure,
                bleu_score,
                bert_score
            ])
        }
    
    def batch_evaluate(self, generated_texts: List[str], 
                      reference_texts: List[str]) -> Dict:
        """Evalúa lote de textos y proporciona estadísticas agregadas"""
        
        individual_scores = []
        for gen, ref in zip(generated_texts, reference_texts):
            scores = self.evaluate_response(gen, ref)
            individual_scores.append(scores)
        
        # Calcular estadísticas agregadas
        metrics = list(individual_scores[0].keys())
        aggregate_scores = {}
        
        for metric in metrics:
            values = [score[metric] for score in individual_scores]
            aggregate_scores[f"{metric}_mean"] = np.mean(values)
            aggregate_scores[f"{metric}_std"] = np.std(values)
            aggregate_scores[f"{metric}_min"] = np.min(values)
            aggregate_scores[f"{metric}_max"] = np.max(values)
        
        return {
            "individual_scores": individual_scores,
            "aggregate_scores": aggregate_scores,
            "total_samples": len(generated_texts)
        }
```

### 2. Evaluación Semántica Avanzada

**Framework de evaluación basado en embeddings:**
```python
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np
from typing import List, Dict

class SemanticEvaluationFramework:
    def __init__(self, model_name: str = "all-MiniLM-L6-v2"):
        self.model = SentenceTransformer(model_name)
        
    def semantic_similarity(self, text1: str, text2: str) -> float:
        """Calcula similitud semántica entre dos textos"""
        
        embeddings = self.model.encode([text1, text2])
        similarity = cosine_similarity([embeddings[0]], [embeddings[1]])[0][0]
        return float(similarity)
    
    def evaluate_factual_consistency(self, generated: str, source_facts: List[str]) -> Dict:
        """Evalúa consistencia factual contra hechos fuente"""
        
        # Generar embeddings
        generated_embedding = self.model.encode([generated])
        fact_embeddings = self.model.encode(source_facts)
        
        # Calcular similitudes
        similarities = cosine_similarity(generated_embedding, fact_embeddings)[0]
        
        # Métricas de consistencia
        max_similarity = np.max(similarities)
        avg_similarity = np.mean(similarities)
        consistency_score = np.mean(similarities > 0.7)  # Threshold empírico
        
        return {
            "max_fact_similarity": float(max_similarity),
            "avg_fact_similarity": float(avg_similarity),
            "consistency_score": float(consistency_score),
            "supporting_facts_count": int(np.sum(similarities > 0.7)),
            "contradicting_facts_count": int(np.sum(similarities < 0.3))
        }
    
    def evaluate_topic_relevance(self, generated: str, topic_keywords: List[str]) -> Dict:
        """Evalúa relevancia del contenido generado al tópico"""
        
        # Embeddings para texto generado y keywords
        generated_embedding = self.model.encode([generated])
        keyword_embeddings = self.model.encode(topic_keywords)
        
        # Similitudes con keywords
        similarities = cosine_similarity(generated_embedding, keyword_embeddings)[0]
        
        # Métricas de relevancia
        relevance_score = np.mean(similarities)
        covered_topics = np.sum(similarities > 0.5)
        topic_coverage = covered_topics / len(topic_keywords)
        
        return {
            "relevance_score": float(relevance_score),
            "topic_coverage": float(topic_coverage),
            "covered_topics_count": int(covered_topics),
            "total_topics": len(topic_keywords),
            "max_keyword_similarity": float(np.max(similarities))
        }
```

## Casos de Uso Específicos por Industria

### 1. Financial Services

**Framework especializado para servicios financieros:**
```python
class FinancialServicesPromptFramework:
    def __init__(self):
        self.compliance_checker = FinancialComplianceChecker()
        self.risk_assessor = RiskAssessment()
        
    def create_investment_analysis_prompt(self, company_data: Dict) -> str:
        """Crea prompt para análisis de inversión con compliance financiero"""
        
        prompt = f"""
You are a senior investment analyst with CFA certification. Analyze the following company for investment potential.

IMPORTANT COMPLIANCE NOTES:
- This analysis is for internal use only
- Do not provide specific buy/sell recommendations
- Include appropriate risk disclaimers
- Ensure all calculations are clearly explained

Company Data:
{json.dumps(company_data, indent=2)}

Provide analysis in the following structure:
1. Executive Summary
2. Financial Health Assessment  
3. Market Position Analysis
4. Risk Factors
5. Investment Considerations
6. Required Disclaimers

Ensure all analysis follows SEC guidelines for investment research.
"""
        return prompt
    
    def create_risk_assessment_prompt(self, portfolio_data: Dict) -> str:
        """Crea prompt para evaluación de riesgo de cartera"""
        
        prompt = f"""
As a qualified risk management professional, assess the risk profile of this portfolio.

Portfolio Data:
{json.dumps(portfolio_data, indent=2)}

Analysis Requirements:
1. Calculate key risk metrics (VaR, CVaR, Sharpe Ratio)
2. Identify concentration risks
3. Assess market, credit, and operational risks
4. Provide risk-adjusted return analysis
5. Recommend risk mitigation strategies

Include methodology explanations and confidence intervals for all calculations.
Ensure compliance with Basel III requirements where applicable.
"""
        return prompt
```

### 2. Healthcare

**Framework para aplicaciones de salud:**
```python
class HealthcarePromptFramework:
    def __init__(self):
        self.hipaa_validator = HIPAAValidator()
        self.medical_disclaimer = MedicalDisclaimerGenerator()
        
    def create_clinical_summary_prompt(self, patient_data: Dict) -> str:
        """Crea prompt para resumen clínico con protección HIPAA"""
        
        # Validar que no hay PHI identificable
        self.hipaa_validator.validate_data(patient_data)
        
        prompt = f"""
You are a clinical documentation specialist. Create a structured clinical summary.

CRITICAL MEDICAL DISCLAIMERS:
- This is for documentation purposes only
- Not intended to replace clinical judgment
- All decisions must be validated by licensed healthcare providers
- Ensure patient privacy protection

De-identified Clinical Data:
{self.anonymize_patient_data(patient_data)}

Required Output Structure:
1. Chief Complaint
2. History of Present Illness
3. Assessment and Plan
4. Differential Diagnoses
5. Recommended Follow-up

Include ICD-10 codes where appropriate and follow clinical documentation standards.
"""
        return prompt
    
    def create_drug_interaction_prompt(self, medications: List[str]) -> str:
        """Crea prompt para análisis de interacciones medicamentosas"""
        
        prompt = f"""
As a clinical pharmacist, analyze potential drug interactions for this medication list.

IMPORTANT: This analysis is for educational purposes only and must be reviewed by a licensed pharmacist.

Medications:
{', '.join(medications)}

Analysis Required:
1. Identify potential drug-drug interactions
2. Assess severity levels (Major, Moderate, Minor)
3. Explain mechanisms of interaction
4. Suggest monitoring parameters
5. Recommend alternative medications if needed

Include references to peer-reviewed literature and follow FDA guidelines.
"""
        return prompt
```

### 3. Legal Tech

**Framework para aplicaciones legales:**
```python
class LegalTechPromptFramework:
    def __init__(self):
        self.attorney_privilege_checker = AttorneyPrivilegeChecker()
        self.jurisdiction_validator = JurisdictionValidator()
        
    def create_contract_analysis_prompt(self, contract_text: str, jurisdiction: str) -> str:
        """Crea prompt para análisis de contratos con consideraciones legales"""
        
        # Validar jurisdicción
        self.jurisdiction_validator.validate(jurisdiction)
        
        prompt = f"""
You are a legal analyst specializing in contract review for {jurisdiction} jurisdiction.

LEGAL DISCLAIMERS:
- This analysis does not constitute legal advice
- Review by licensed attorney required
- Jurisdiction-specific laws apply
- Client confidentiality must be maintained

Contract Analysis Requirements:
1. Identify key terms and conditions
2. Flag potential legal risks
3. Assess enforceability under {jurisdiction} law
4. Highlight missing standard clauses
5. Suggest areas for negotiation

Contract Text:
{contract_text}

Provide analysis following standard legal review protocols.
"""
        return prompt
    
    def create_legal_research_prompt(self, legal_question: str, jurisdiction: str) -> str:
        """Crea prompt para investigación legal estructurada"""
        
        prompt = f"""
Conduct legal research on the following question for {jurisdiction} jurisdiction.

Legal Question: {legal_question}

Research Structure:
1. Relevant Statutes and Regulations
2. Case Law Analysis
3. Legal Precedents
4. Current Legal Trends
5. Practical Implications

IMPORTANT:
- Cite all sources with proper legal citation format
- Distinguish between binding and persuasive authority
- Note any recent changes in law
- This research is for informational purposes only

Provide comprehensive analysis with proper legal reasoning.
"""
        return prompt
```

## Integración con Herramientas de Productividad

### 1. Slack Bot Integration

**Bot de Slack para prompt engineering:**
```python
from slack_bolt import App
from slack_bolt.adapter.socket_mode import SocketModeHandler

class PromptEngineeringSlackBot:
    def __init__(self, slack_bot_token: str, slack_app_token: str):
        self.app = App(token=slack_bot_token)
        self.prompt_manager = MultiModelOrchestrator()
        
        # Registrar comandos
        self.setup_commands()
        
    def setup_commands(self):
        """Configura comandos del bot"""
        
        @self.app.command("/analyze")
        def handle_analyze_command(ack, respond, command):
            ack()
            
            # Extraer parámetros
            text = command['text']
            user_id = command['user_id']
            
            # Ejecutar análisis
            result = self.prompt_manager.route_prompt(
                f"Analyze the following business scenario: {text}",
                {"user_id": user_id, "platform": "slack"}
            )
            
            respond(f"Analysis Results:\n```{result['content']}```")
        
        @self.app.command("/prompt-test")
        def handle_prompt_test(ack, respond, command):
            ack()
            
            # Permitir testing de prompts en tiempo real
            prompt_text = command['text']
            
            # Ejecutar con múltiples modelos
            results = {}
            for model in ['gpt-4o', 'claude-3-5-sonnet', 'gemini-pro']:
                try:
                    result = self.prompt_manager.route_prompt(
                        prompt_text, 
                        {"preferred_model": model}
                    )
                    results[model] = result['content'][:200] + "..."
                except Exception as e:
                    results[model] = f"Error: {str(e)}"
            
            response = "Prompt Test Results:\n"
            for model, result in results.items():
                response += f"\n*{model}:*\n{result}\n"
            
            respond(response)
    
    def start(self):
        """Inicia el bot"""
        handler = SocketModeHandler(self.app, self.slack_app_token)
        handler.start()
```

### 2. Microsoft Teams Integration

**Integración con Microsoft Teams:**
```python
from botbuilder.core import ActivityHandler, TurnContext, MessageFactory
from botbuilder.schema import ChannelAccount

class TeamsPromptBot(ActivityHandler):
    def __init__(self):
        self.prompt_manager = MultiModelOrchestrator()
        
    async def on_message_activity(self, turn_context: TurnContext):
        """Maneja mensajes entrantes"""
        
        user_message = turn_context.activity.text
        
        # Detectar comandos especiales
        if user_message.startswith("/prompt"):
            await self.handle_prompt_command(turn_context, user_message)
        elif user_message.startswith("/analyze"):
            await self.handle_analysis_command(turn_context, user_message)
        else:
            # Respuesta general usando AI
            response = self.prompt_manager.route_prompt(
                f"Respond helpfully to: {user_message}",
                {"platform": "teams", "context": "business"}
            )
            
            await turn_context.send_activity(
                MessageFactory.text(response['content'])
            )
    
    async def handle_prompt_command(self, turn_context: TurnContext, command: str):
        """Maneja comando de prompt específico"""
        
        prompt_text = command.replace("/prompt", "").strip()
        
        # Crear card adaptivo para mostrar resultados
        card = {
            "type": "AdaptiveCard",
            "version": "1.3",
            "body": [
                {
                    "type": "TextBlock",
                    "text": "Prompt Results",
                    "weight": "Bolder",
                    "size": "Medium"
                },
                {
                    "type": "TextBlock",
                    "text": self.prompt_manager.route_prompt(prompt_text, {})['content'],
                    "wrap": True
                }
            ]
        }
        
        attachment = MessageFactory.attachment({
            "contentType": "application/vnd.microsoft.card.adaptive",
            "content": card
        })
        
        await turn_context.send_activity(attachment)
```

---

Esta sección completa proporciona un ecosistema integral de herramientas, frameworks y patrones para implementar prompt engineering profesional a escala empresarial, cubriendo desde proveedores cloud hasta integraciones de productividad.
